In TVB different monitors for various modalities are specified as classes contained in the `monitors.py` file. These enable the measurement of simulation data, and modality specific monitors can operate on input to produce data according with the relevant physical measurement process. 

```
class Bold(Monitor):
    """
    Base class for the Bold monitor.
    **Attributes**
        hrf_kernel: the haemodynamic response function (HRF) used to compute
                    the BOLD (Blood Oxygenation Level Dependent) signal.
        length    : duration of the hrf in seconds.
        period    : the monitor's period
...
        # At the monitor's period, apply the heamodynamic response function to
        # the stock and return the resulting BOLD signal.
        if step % self.istep == 0:
            time = step * self.dt
            hrf = numpy.roll(self.hemodynamic_response_function,
                             ((step // self._interim_istep % self._stock_steps) - 1),
                             axis=1)
            if isinstance(self.hrf_kernel, equations.FirstOrderVolterra):
                k1_V0 = self.hrf_kernel.parameters["k_1"] * self.hrf_kernel.parameters["V_0"]
                bold = (numpy.dot(hrf, self._stock.transpose((1, 2, 0, 3))) - 1.0) * k1_V0
            else:
                bold = numpy.dot(hrf, self._stock.transpose((1, 2, 0, 3)))
            bold = bold.reshape(self._stock.shape[1:])
            return [time, bold]
```
From `monitors.py`

For fNIRS we need to add HbO/HbR as outputs to the BOLD monitor above and HRF equations.
```
class FirstOrderVolterra(HRFKernelEquation):
    """
    Integral form of the first Volterra kernel of the three used in the
    Ballon Windekessel model for computing the Bold signal.
    This function describes a damped Oscillator.
    **Parameters** :
    * :math:`\\tau_s`: Dimensionless? exponential decay parameter.
    * :math:`\\tau_f`: Dimensionless? oscillatory parameter.
    * :math:`k_1`    : First Volterra kernel coefficient.
    * :math:`V_0` : Resting blood volume fraction.
    **References** :
    .. [F_2000] Friston, K., Mechelli, A., Turner, R., and Price, C., *Nonlinear
        Responses in fMRI: The Balloon Model, Volterra Kernels, and Other
        Hemodynamics*, NeuroImage, 12, 466 - 477, 2000.
    """

    equation = Final(
        label="First Order Volterra Kernel",
        default="""1/3. * exp(-0.5*(var / tau_s)) * (sin(sqrt(1./tau_f - 1./(4.*tau_s**2)) * var)) / """
                """(sqrt(1./tau_f - 1./(4.*tau_s**2)))""",
        doc=""":math:`G(t - t^{\\prime}) =
             e^{\\frac{1}{2} \\left(\\frac{t - t^{\\prime}}{\\tau_s} \\right)}
             \\frac{\\sin\\left((t - t^{\\prime})
             \\sqrt{\\frac{1}{\\tau_f} - \\frac{1}{4 \\tau_s^2}}\\right)}
             {\\sqrt{\\frac{1}{\\tau_f} - \\frac{1}{4 \\tau_s^2}}}
             \\; \\; \\; \\; \\; \\;  for \\; \\; \\; t \\geq t^{\\prime}
             = 0 \\; \\; \\; \\; \\; \\;  for \\; \\; \\;  t < t^{\\prime}`.""")

    parameters = Attr(
        field_type=dict,
        label="Mixture of Gammas Parameters",
        default=lambda: {"tau_s": 0.8, "tau_f": 0.4, "k_1": 5.6, "V_0": 0.02})
```
From `equations.py`

These equations will be adapted using haemodynamic and optics equations from the development of fNIRS forward models in Dynamic Causal Modelling (DCM) (Tak et al., 2015).

<div align="center">
<img width="217" alt="image" src="https://user-images.githubusercontent.com/49303905/174488814-9480b23d-c904-4862-b21d-c439e347e33d.png">

Schematic of the generative model of fNIRS data (Tak et al., 2015) 
</div>


>> (Note: reduced-Wong-Wang & Jansen-Rit used for the **neurodynamic equation**)<br>
>> The **hemodynamic equation** uses the Balloon model and its extensions to describe how neural activity causes a change in a flow inducing signal which in turn causes an increase in blood flow with concomitant changes in relative blood volume and deoxy-hemoglobin. 
>> The **optics equation** uses a sensitivity matrix, S, describing how changes in hemodynamic sources cause changes in optical measurements. Potential pial vein contamination of the fNIRS measurements is corrected using matrices WH and WQ. Spatially distributed hemodynamic source is generated using Gaussian spatial smoothing kernel K (Tak et al., 2015). 

The EEG monitor needs to be adapted to allow for projection to fNIRS channels - edges in a source * detector graph (S-D pairs), rather than single channel locations, requiring the creation of a new class `fNIRS(Projection)`. For the Kernel Flow there is a template montage for channel locations in MNI space and an EEG 10/20 reference frame, and precomputed projection matrices. 

```
class EEG(Projection):
...
    projection = Attr(
        projections_module.ProjectionSurfaceEEG,
        default=None, label='Projection matrix',
        doc='Projection matrix to apply to sources.')

    reference = Attr(
        str, required=False,
        label="EEG Reference",
        doc='EEG Electrode to be used as reference, or "average" to '
            'apply an average reference. If none is provided, the '
            'produced time-series are the idealized or reference-free.')

    sensors = Attr(sensors_module.SensorsEEG, required=True, label="EEG Sensors",
                   doc='Sensors to use for this EEG monitor')
...
    @classmethod
    def from_file(cls, sensors_fname='eeg_brainstorm_65.txt', projection_fname='projection_eeg_65_surface_16k.npy',
                  **kwargs):
        return Projection.from_file.__func__(cls, sensors_fname, projection_fname, **kwargs)

    def config_for_sim(self, simulator):
        super(EEG, self).config_for_sim(simulator)
        self._ref_vec = numpy.zeros((self.sensors.number_of_sensors,))
        if self.reference:
            if self.reference.lower() != 'average':
                sensor_names = self.sensors.labels.tolist()
                self._ref_vec[sensor_names.index(self.reference)] = 1.0
            else:
                self._ref_vec[:] = 1.0 / self.sensors.number_of_sensors
        self._ref_vec_mask = numpy.isfinite(self.gain).all(axis=1)
        self._ref_vec = self._ref_vec[self._ref_vec_mask]
...
    def sample(self, step, state):
        maybe_sample = super(EEG, self).sample(step, state)
        if maybe_sample is not None:
            time, sample = maybe_sample
            sample -= self._ref_vec.dot(sample[:, self._ref_vec_mask])[:, numpy.newaxis]
            return time, sample.reshape((state.shape[0], -1, 1))

    def create_time_series(self, connectivity=None, surface=None,
                           region_map=None, region_volume_map=None):
        return TimeSeriesEEG(sensors=self.sensors,
                             sample_period=self.period,
                             title=' ' + self.__class__.__name__)
```
From `monitors.py`

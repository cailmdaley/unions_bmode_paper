# Data Section Scaffold
- **Survey context**  
  - Introduce UNIONS as the wide-area $ugriz$ imaging program delivering a $6250\,\mathrm{deg}^2$ northern footprint with $10\sigma$ depths of $[23.7, 24.5, 24.2, 23.8, 23.3]$ in the respective bands, citing Gwyn et al. (2025) for the instrument suite and science drivers.  
  - Emphasise that weak-lensing shear measurements draw primarily on the CFIS $r$-band exposures processed through ShapePipe, whose recent upgrades include a metacalibration-ready weight definition, a lower detection threshold, and a transition to the MCCD PSF model.  
  - Reference earlier cosmology analyses (e.g., Guinot et al.; Goh et al.) that covered approximately $1600\,\mathrm{deg}^2$, noting that the current catalogue release doubles the effective area and therefore mandates a fresh systematic audit.

- **Catalogue variants**  
  - Describe the six shear catalogues entering the validation: the baseline SP\_v1.4.5 set, its leakage-corrected counterpart, the size-cut catalogue SP\_v1.4.6 and its corrected version, plus the bright-and-faint-star masked SP\_v1.4.8 release and its corrected partner.  
  - Explain that leakage-corrected catalogues replace the ellipticity columns with solutions of the PSF leakage model derived from $\rho$/$\tau$ statistics, leaving all other selections untouched.  
  - Summarise the scientific motivation behind the successive catalogue updates: v1.4.6 enforces stricter size and signal-to-noise requirements to stabilise shape noise; v1.4.8 extends stellar halo masking to suppress additive artefacts at the cost of $\approx14\%$ area loss.

- **Survey property snapshot**  
  - Provide a table listing for each catalogue: footprint area, effective number density, and ellipticity dispersion as measured in the October 2025 survey-stat refresh (e.g., $A=2582\,\mathrm{deg}^2$, $n_{\mathrm{eff}}=7.75\,\mathrm{arcmin}^{-2}$, $\sigma_e=0.438$ for SP\_v1.4.5; $A=2405\,\mathrm{deg}^2$, $n_{\mathrm{eff}}=6.13\,\mathrm{arcmin}^{-2}$, $\sigma_e=0.380$ for SP\_v1.4.6; $A=2049\,\mathrm{deg}^2$, $n_{\mathrm{eff}}=6.12\,\mathrm{arcmin}^{-2}$, $\sigma_e=0.380$ for SP\_v1.4.8).  
  - Include ancillary notes on PSF star densities where available and highlight how the size-cut and masking updates perturb these summary metrics.  
  - Add a reminder to verify the numbers against the latest CosmologyValidation survey-stat calculations before finalising the manuscript.

- **Tomography and redshift inputs**  
  - State that the present validation focuses on a single source bin spanning the full shear catalogue, with photometric-redshift distributions taken from the blinded set delivered for cosmological inference (blind A for the fiducial configuration).  
  - Mention that per-catalogue redshift calibration files feed both the covariance generation and the model predictions used downstream.

- **Diagnostic products**  
  - Note the availability of full-footprint visualisations, leak-corrected shape diagnostics, and ancillary PSF statistics compiled during the October 2025 analysis sprint; flag any plots that should accompany the data description (e.g., footprint maps, histograms of weights and ellipticity dispersion).  
  - Insert TODOs to ensure that final figures illustrate the footprint inheritance across catalogue versions and to decide whether to include a comparison of $\rho$/$\tau$ residuals for the leakage-corrected samples.


# Methods Section Scaffold
## Overview of the validation chain
- Lay out the scientific workflow: measure the two-point shear correlation functions, transform them into pure E/B/ambiguous components, quantify B-mode consistency via both real-space and COSEBIs statistics, propagate covariance uncertainties, and cross-check in harmonic space.  
- Stress that every step mirrors the blinded cosmology pipeline, ensuring that the validation speaks directly to the scales and statistics used for parameter inference.

## Catalogue preparation and leakage mitigation
- Recap the selection applied to the shear catalogues (metacalibration weights, masking, size thresholds) and describe how the leakage-corrected variants substitute PSF-corrected ellipticities based on the fiducial $\rho$/$\tau$ fits.  
- Summarise the qualitative impact of the leakage correction observed in prior analyses: reduced large-scale additive power and improved $\xi_+$ consistency, with more modest gains for $\xi_-$.  
- Add a placeholder to cite the leakage-coefficient measurements once the numerical values are extracted from the diagnostics.

## Two-point correlation measurements
- Document the measurement of $\xi_\pm(\theta)$ in 20 logarithmic bins spanning $1$–$250$ arcmin, computed with the standard tangential and cross-shear estimators and metacalibration weights.  
- Explain the role of an extended integration grid ($0.5$–$500$ arcmin with 1000 bins) used to evaluate the integral transforms required for pure-mode statistics and COSEBIs.  
- Clarify that the correlation functions are evaluated for each catalogue version using identical binning and weighting so that any differences trace back to survey selections rather than measurement settings.

## Pure E/B/ambiguous decomposition
- Describe the transformation of $\xi_\pm$ into pure E-, B-, and ambiguous modes following the formalism of Schneider et al. (2022), implemented via the publicly available [cosmo\_numba](https://github.com/CosmoStat/cosmo_numba) kernels.  
- Note that the transformation preserves information from both correlation functions and yields per-bin covariance blocks for the E, B, and ambiguous components.  
- Highlight the scientific interpretation: B modes diagnose additive systematics, E modes feed cosmological inference, and ambiguous modes monitor information loss when scale cuts are imposed.

## Semi-analytic covariance propagation
- Explain that CosmoCov provides Gaussian covariance matrices for the integration binning using the survey properties listed above and a KiDS-Legacy-like fiducial cosmology.  
- Detail the Monte Carlo propagation: draw 2000 realisations of $\xi_\pm$ from the CosmoCov covariance, transform each into pure E/B/ambiguous space, and estimate the resulting covariance, which captures correlations between all mode components.  
- Acknowledge that non-Gaussian terms are currently omitted because of prohibitive computational cost, citing the discussion in the semi-analytic covariance notes about the expected impact (larger diagonal variance, additional off-diagonal structure).  
- Flag the need to assess whether the absence of non-Gaussian contributions biases PTE estimates, and propose a TODO to quantify this once updated covariances become available.

## Scale-cut strategy and PTE evaluation
- Outline the construction of two-dimensional PTE heatmaps that scan all combinations of lower and upper angular cuts across the 20 reporting bins for both $\xi_+$ and $\xi_-$.  
- Mention that the Hartlap correction is applied using the effective number of Monte Carlo samples to debias the $\chi^2$ statistics derived from the propagated covariance.  
- Summarise key findings from the August–October 2025 investigations: $\xi_+$ remains consistent with zero B modes across all scales once the semi-analytic covariance is used, whereas $\xi_-$ prefers upper scale cuts around $30$–$40$ arcmin unless leakage corrections are applied.  
- Include checklist items for the writer to extract the fiducial PTEs (e.g., $[5,85]$ arcmin for $\xi_+$, $[15,85]$ arcmin for $\xi_-$) and to describe any residual islands of acceptable scale combinations.

## COSEBIs analysis
- Describe the computation of COSEBI modes on the $[5,144]$ arcmin interval, retaining at least six modes to match the blinded inference pipeline.  
- Explain that the same CosmoCov integration covariance feeds the COSEBIs transformation, generating mode covariances and PTE values for each scale-cut combination.  
- Highlight how the COSEBI B-mode patterns complement the real-space tests by revealing oscillatory signatures characteristic of repeating additive systematics.  
- Add TODOs to report the fiducial six-mode B-mode PTE for each catalogue and to comment on any high-order mode behaviour (e.g., alternating-sign sequences).

## Harmonic-space cross-checks
- Summarise the pseudo-$C_\ell$ validation: convert shear catalogues to HEALPix maps at $N_{\mathrm{side}}=1024$, estimate coupled spectra, and decouple them to obtain $C_\ell^{EE}$, $C_\ell^{EB}$, and $C_\ell^{BB}$ across 32 bandpowers.  
- Mention that random-rotation noise realisations provide an empirical noise covariance, enabling checks for excess $C_\ell^{BB}$ power that might not be apparent in configuration space.  
- Provide a reminder to decide whether the pseudo-$C_\ell$ results are mature enough for inclusion; if not, document that they remain an internal consistency check.


# Results Section Scaffold
## Pure E/B diagnostics
- For each catalogue (baseline, leakage-corrected, size-cut, masked), summarise:  
  - The amplitude and uncertainty of $\xi_+^B$ and $\xi_-^B$ across the full angular range, highlighting any scales that exceed the statistical envelope.  
  - The fiducial PTE values derived from the propagated covariance and the preferred scale-cut windows suggested by the heatmaps.  
  - The behaviour of ambiguous modes and whether they signal information loss when cuts are applied.  
- Emphasise comparative statements: e.g., leakage correction substantially raises the $\xi_+$ PTE; size cuts shrink small-scale error bars; expanded masking alters large-scale variance.

## COSEBIs results
- Report, for each catalogue, the B-mode PTE associated with the fiducial six-mode configuration and note any deviations in higher-order modes that match known systematics signatures.  
- Compare the COSEBI findings to the real-space diagnostics, commenting on coherent trends (e.g., both tests flag $\xi_-$ at large scales) or discrepancies that warrant deeper investigation.  
- Encourage inclusion of visual summaries (mode amplitudes versus index, PTE heatmaps) in the final manuscript to illustrate these comparisons.

## Covariance behaviour
- Discuss how the semi-analytic covariance reshapes the uncertainty budget relative to previous jackknife estimates: smoother diagonal trends, reduced noise in off-diagonal blocks, and scale-dependent changes in error amplitudes.  
- Describe catalogue-to-catalogue differences: the masked sample’s covariance shows inflated large-scale variance; size cuts narrow small-scale errors.  
- Suggest quantifying the impact on cosmological parameter constraints (e.g., expected shift in $S_8$ uncertainty) once the validation feeds into the inference pipeline.

## Harmonic-space findings (if included)
- Summarise whether the $C_\ell^{BB}$ spectra remain consistent with noise for each catalogue and how any detected excesses map onto the configuration-space anomalies.  
- Note any multipole ranges that demand closer scrutiny (e.g., low-$\ell$ upturns linked to residual masking artefacts).  
- If harmonic-space diagnostics are deferred, state this explicitly to avoid leaving the reader expecting results.

## Interpretation checklist
- Identify which catalogue (and leakage treatment) satisfies all B-mode criteria without additional scale cuts and therefore qualifies as the fiducial choice for cosmology.  
- Highlight any remaining tensions (especially in $\xi_-$ or COSEBI high modes) and describe the proposed mitigation (tighter cuts, mask adjustments, further leakage modelling).  
- Comment on how the semi-analytic covariance affects the perceived significance of B modes and whether it resolves former discrepancies rooted in noisy jackknife errors.


# Outstanding Actions for the Writer
- [ ] Populate all quantitative entries: survey-property table values, B-mode amplitudes, PTEs, preferred scale cuts, and COSEBI mode statistics.  
- [ ] Insert citations to the relevant methodology papers (Schneider et al. 2010, 2012, 2022 for pure modes and COSEBIs; Asgari et al. for survey comparisons) alongside Gwyn et al. (2025).  
- [ ] Decide whether to include harmonic-space diagnostics and, if so, draft the corresponding narrative.  
- [ ] Extract leakage coefficients and residual PSF correlation amplitudes from the latest analyses to substantiate statements about the corrected catalogues.  
- [ ] Ensure that every figure referenced in the narrative is available in the B-mode diagnostic deck or reproduced in the paper’s figure set.  
- [ ] Reconfirm survey-property values and covariance settings just before submission to reflect any late-stage catalogue updates.

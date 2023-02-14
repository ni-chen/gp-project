# 

Differentiable holographic imaging

[TOC]

---------

## Project target

> - Retrieve complex fields from one single intensity image, while considering the system model sensitivities (imperfections), system and object constraints.
>
> - Make a framework that applies to different types of objects by choosing user-defined constraints
>
> - larger phase delay



----

Hologram is  $I(r) = |R _z(r) + u _z(r)| ^2$  where $u _z(r) = \int _{z _1} ^{z _2} o(r, z) \otimes h _0(r, z) dz$ and $\otimes$ denotes convolution.


In compressive holography, we have linear approximation of 
$$
I(r) = | r _z(r) | ^2 - 2 \Re \{ u _z(r) \} + | u _z(r) | ^2 \approx 1- 2 \Re \{ u _z(r) \overline{r _z (r) } \}
$$
which assume that the power of the reference illumination is much more stronger than the object beam, i.e., the objects hinders only a little light. For opacity objects, this means that the objects are sparse (particles or 2D objects with transparent background).




--------------------


We solve this problem without any approximation by automatic differential approach:


- The forward model: holography recording which is non-linear.
- Prior: energy conservation (absorption  $\geq$ 0 )


The illumination wave is not ideal plane wave, we suppose it is $A_{illum}\exp\{ j \Phi_{illum} \}$.



------



## AD optimization

### [Optimizing with constraints: reparametrization and geometry.](https://vene.ro/blog/mirror-descent.html)


- [Reparametrization.](https://vene.ro/blog/mirror-descent.html#reparametrization)
- [Projected gradient.](https://vene.ro/blog/mirror-descent.html#projected-gradient)




---------------------------------------------

### 3D/Complex object modeling


> - $n_b$: background refractive index  of surrounding media
> - $k_b$: background
> - $n$: object refractive index
> - $k$: object
> - $k_0 = \frac{2 \pi}{\lambda}$: free-space 
> - $T$: medium transmittance in $[0,1]$
>
> - $o_{vol}(x,y,z)$: 3D object matrix that is normalized to $[0,1]$




- **Set volume refractive index**
  $$
  \begin{equation}
  \begin{aligned}
  n_{obj} = 
  \begin{cases}
  n  & o_{vol}\neq 0  &\text{gray level????} \\
  n_b & o_{vol}= 0
  \end{cases}  
  \end{aligned}
  \end{equation}
  $$


- **Set volume absorption**
  $$
  \begin{equation}
  \begin{aligned}
  \alpha_{obj} = 
  \begin{cases}
  (2 - \log_{10}(T \times 100)) \times o_{vol}  &   \text{Positive illumination}   \\
  (2 - \log_{10}(T \times o_{vol} \times 100))   &   \text{Negative illumination}   \\
  \end{cases}  
  \end{aligned}
  \end{equation}
  $$


-  **Convert refractive index ($n$) and medium transmittance ($T$) to complex transfer function**
  
  - object image is normalized to $[0,1]$


$$
\begin{equation}
\begin{aligned}
t_{obj} = \exp\left[-\alpha_{obj} \right] \times \exp\left(-j\frac{2 \pi}{\lambda} (n_{bg} - n_{obj})\Delta z \right)
\end{aligned}
\end{equation}
$$

### Optimization formulation

$t=|t|\exp(j \phi(t)) = \Re(t) + j \Im(t)$, where $|t| \in [0, 1]$, $\Phi(t) \in [-\pi, \pi]$.

$b= |P(t)|^2$

$$
\begin{equation}
\begin{aligned}
& \underset{\mathbf{o} _t}{\text{min}}
& &  
\sum _{n=1} ^{n=N} \| \mathbf{P t}_n  - \mathbf{b} _n \| _2 ^2 + \beta_1 \mathcal{R}_{l1}( \mathbf{t}) + \beta_2 \mathcal{R}_{TV}^{\epsilon}(\mathbf{t})  \\\\
& s.t. & & \abs{\mathbf{t}} -1 \leq 0  
\end{aligned}
\end{equation}
$$



##### Regularization for real and imag


- **Sparsity**

  - Positive illumination

  $$
  \begin{equation}
  \begin{aligned}
  \| x \|_1^1 =
  \begin{cases}
  \|1 - |x| \|_1^1 +\|1 - \Re(x) \|_1^1     &x \text{ is amplitude only} \\
  \|1 - \Re(x) \|_1^1 +\| \Im(x) \|_1^1    &x \text{ is phase only} \\
  \|1 - |x| \|_1^1 +\|1 - \Re(x) \|_1^1  +\|\Im(x) \|_1^1    &\text{ Otherwise} \\
  \end{cases}  
  \end{aligned}
  \end{equation}
  $$

  - Negative illumination

  $$
  \begin{equation}
  \begin{aligned}
  \| x \|_1^1 =
  \begin{cases}
  \||x| \|_1^1 +\|\Re(x) \|_1^1     & x \text{ is amplitude only} \\
  
  \||x| \|_1^1 +\|\Re(x) \|_1^1  +\|\Im(x) \|_1^1    & \text{ Otherwise} \\
  \end{cases}  
  \end{aligned}
  \end{equation}
  $$


- **Total variation**


$$
\begin{equation}
\begin{aligned}
{TV}(x) =
\begin{cases}
TV(|x|)    &x \text{ is amplitude only} \\
TV(\Re{(x)} + \Im(x))    &\text{Otherwise} \\
\end{cases}  
\end{aligned}
\end{equation}
$$



#### Regularization for mag and phase


- **Sparsity**

  $$
  \begin{equation}
  \begin{aligned}
  \mathcal{R}_{l1}( \mathbf{t}) =
  \begin{cases}
  \| 1 - |t| \|_1^1      & \text{ positive illumination}  \\
  \| t \|_1^1      & \text{ negative illumination} \\
  \end{cases}  
  \end{aligned}
  \end{equation}
  $$



- **Total variation**


$$
\begin{equation}
\begin{aligned}
\mathcal{R}_{TV}^{\epsilon}( \mathbf{x}) =
\begin{cases}
\sum\limits_k\sqrt{(\nabla_x \Re(t))^2 + (\nabla_y \Re(t))^2 + \epsilon ^2}   &t \text{ is amplitude only} \\
\sum\limits_k\sqrt{(\nabla_x \Re(t))^2 + (\nabla_y \Re(t))^2 + \nabla_x \Im(t))^2 + (\nabla_y \Im(t))^2 + \epsilon ^2}   &\text{Otherwise} \\
\end{cases}  
\end{aligned}
\end{equation}
$$

==The regularization of only applied to  $|x|$==

Solution: Amplitude and phase split regularization

For phase, we regularize $\exp(j \Phi(t))$  instead of $\Phi(t)$
$$
\begin{equation}
\begin{aligned}
  & \mathcal{R}_{l1}(t)  \\ 
= &\mathcal{R}_{l1}(|t|) + \mathcal{R}_{l1}(\Phi(t)) = 
\begin{cases}
\mathcal{R}_{l1}(1-|t|) + \mathcal{R}_{l1}( \Im(\exp(j \Phi(t)) & \text{ positive}   \\\\
\mathcal{R}_{l1}(|t|) + \mathcal{R}_{l1}( \Im(\exp(j \Phi(t))) + \mathcal{R}_{l1}( 1-\Re(\exp(j\Phi(t)))  & \text{ negative} 
\end{cases}
\end{aligned}
\end{equation}
$$


$$
\begin{equation}
\begin{aligned}
& \underset{\mathbf{o} _t}{\text{min}}
& &  
\sum _{n=1} ^{n=N} \| \mathbf{P t}_n  - \mathbf{b} _n \| _2 ^2 + \beta_1 \mathcal{R}_{l1}(|\mathbf{t}|) + \beta_2 \mathcal{R}_{l1}(\exp(j \Phi(\mathbf{t})) 
+ \beta_3 \mathcal{R}_{TV}^{\epsilon}(|\mathbf{t}|) + \beta_4 \mathcal{R}_{TV}^{\epsilon}(\exp(j \Phi(\mathbf{t})) \\\\
& s.t. & & \abs{\mathbf{t}} -1 \leq 0  
\end{aligned}
\end{equation}
$$



##### Constraints


|                              | $\textcolor{red}{n_{obj}-n}$ | $\textcolor{red}{T_{obj}}$ | amplitude-only | phase only | Positive real | Negative real | Positive imag | Negative imag |
| ---------------------------- | ---------------------------- | -------------------------- | -------------- | ---------- | ------------- | ------------- | ------------- | ------------- |
| $illum ^ + $ | >0                           |                            |                |            | True          |               | True          |               |
|                              | =0                           |                            | True           |            | True          |               |               |               |
|                              | <0                           |                            |                |            | True          |               | True          |               |
|                              |                              | =1                         |                | True       | True          |               | True          |               |
| $illum ^ - $                 | >0                           |                            |                |            | True          |               | True          |               |
|                              | =0                           |                            |                |            | True          |               |               |               |
|                              | <0                           |                            |                |            | True          |               | True          |               |


- common constraints
  - $|x|<=1$,  $|\Im(x)|<=1$, $|\Re(x)|<=1$





----------

## Simulation

solving $y = A x + b$ with AD can benefit from:

- <u>more freedom of A  (composition of some functions)</u>
- misallignment



- Phase wrap issue in complex field reconstruction

|                 |                         $\Phi_{gt}$                          |                        $\Phi_{pred}$                         |                           $h_{gt}$                           |                          $h_{pred}$                          |
| --------------- | :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| $h=0.5 \lambda$ | ![gray_field-RI1.5-T0.5-Illum1_gt_phase](img/gray_field-RI1.5-T0.5-Illum1_gt_phase.png) | ![gray_field-RI1.5-T0.5-Illum1_pred_phase](img/gray_field-RI1.5-T0.5-Illum1_pred_phase.png) | ![gray_field-RI1.5-T0.5-Illum1_height_gt](img/gray_field-RI1.5-T0.5-Illum1_height_gt.png) | ![gray_field-RI1.5-T0.5-Illum1_factor_ad_height_pred](img/gray_field-RI1.5-T0.5-Illum1_factor_ad_height_pred.png) |
| $h=\lambda$     | ![gray_field-RI1.5-T0.5-Illum1_gt_phase_2l](img/gray_field-RI1.5-T0.5-Illum1_gt_phase_2l.png) | ![gray_field-RI1.5-T0.5-Illum1_pred_phase_2l](img/gray_field-RI1.5-T0.5-Illum1_pred_phase_2l.png) | ![gray_field-RI1.5-T0.5-Illum1_height_gt_2l](img/gray_field-RI1.5-T0.5-Illum1_height_gt_2l.png) | ![gray_field-RI1.5-T0.5-Illum1_factor_ad_height_pred_2l](img/gray_field-RI1.5-T0.5-Illum1_factor_ad_height_pred_2l.png) |

let $A = A_1 \times A_2$



|                 | $\Phi_{gt}$ | $\Phi_{pred}$ | $h_{gt}$ | $h_{pred}$ |
| :-------------: | ----------: | ------------: | -------: | ---------: |
| $h=0.5 \lambda$ |             |               |          |            |
|   $h=\lambda$   |             |               |          |            |













## Defocus



- experiment

|                          without z                          |                           with z                            |                          z history                          |
| :---------------------------------------------------------: | :---------------------------------------------------------: | :---------------------------------------------------------: |
| ![image-20220528133102263](img/image-20220528133102263.png) | ![image-20220528143451125](img/image-20220528143451125.png) | ![image-20220528133135797](img/image-20220528133135797.png) |
| ![image-20220528151724758](img/image-20220528151724758.png) | ![image-20220528152032671](img/image-20220528152032671.png) | ![image-20220528152055011](img/image-20220528152055011.png) |
|                                                             |                                                             |                                                             |

practical sample reconstruction

| ![img](img/41598_2021_90312_Fig3_HTML.webp) |
| ------------------------------------------- |

| ![image-20220529081108570](img/image-20220529081108570.png) | ![image-20220529081207208](img/image-20220529081207208.png) |
| ----------------------------------------------------------- | ----------------------------------------------------------- |













# aberration





| ![image-20220605101901119](img/image-20220605101901119.png) |
| ----------------------------------------------------------- |
| ![image-20220605101849865](img/image-20220605101849865.png) |



|               without aberration elimination                |                 with aberration elimination                 |
| :---------------------------------------------------------: | :---------------------------------------------------------: |
| ![image-20220605102728544](img/image-20220605102728544.png) | ![image-20220605102735256](img/image-20220605102735256.png) |



## Extra experiments



|                          amplitude                          |                            phase                            |
| :---------------------------------------------------------: | :---------------------------------------------------------: |
| ![image-20220605151454695](img/image-20220605151454695.png) | ![image-20220605151519053](img/image-20220605151519053.png) |





## Comparison (Multiple-depth method)

![The schematic diagram for the multiple-plane phase retrieval method.... |  Download Scientific Diagram](img/The-schematic-diagram-for-the-multiple-plane-phase-retrieval-method-Yellow-arrows-depict.png)

![image-20220724160759461](img/image-20220724160759461.png)

| ![image-20220724160822332](img/image-20220724160822332.png) | ![image-20220724160829016](img/image-20220724160829016.png) |
| :---------------------------------------------------------: | ----------------------------------------------------------- |



**With translation misalignment**

| ![image-20220904083450049](img/image-20220904083450049.png) | ![image-20220904083459852](img/image-20220904083459852.png) |
| ----------------------------------------------------------- | ----------------------------------------------------------- |



![image-20220724160929202](img/image-20220724160929202.png)

| ![image-20220724161025433](img/image-20220724161025433.png) | ![image-20220724161033946](img/image-20220724161033946.png) |
| ----------------------------------------------------------- | ----------------------------------------------------------- |



- Specifications
  - pixel resolution: $1024\times 1024$
  - pp: 2*3.45um
  - $z_o=50mm$, $\Delta z=10mm$ 
  - $N_z=5$





|          Captured images          |                       reconstruction                        |
| :-------------------------------: | :---------------------------------------------------------: |
| ![MD_capture](img/MD_capture.gif) | ![image-20220828100729641](img/image-20220828100729641.png) |





|                  | 1st                                                         | 5th                                                         |
| ---------------- | ----------------------------------------------------------- | ----------------------------------------------------------- |
| feature maspping | ![image-20220914065656233](img/image-20220914065656233.png) | ![image-20220914065723160](img/image-20220914065723160.png) |
| wrap             | ![image-20220914065802459](img/image-20220914065802459.png) | ![image-20220914065813979](img/image-20220914065813979.png) |











------



### Stat of holograms


| amplitude | ![](img/kaust-RI1.0-T0.0-Illum1_holo_norm.png)               |
| --------- | ------------------------------------------------------------ |
| phase     | ![kaust-RI1.5-T1.0-Illum1_holo_norm](img/kaust-RI1.5-T1.0-Illum1_holo_norm.png) |
| complex   | ![kaust-RI1.5-T0.5-Illum1_holo_norm](img/kaust-RI1.5-T0.5-Illum1_holo_norm.png) |



### Multiple-step phases

- complex


|      |                    $A$                     |                    $\Phi$                    |                    $\Re$                    |                    $\Im$                    |                                             |
| ---- | :----------------------------------------: | :------------------------------------------: | :-----------------------------------------: | :-----------------------------------------: | ------------------------------------------- |
| GT   | ![](img/gray-RI1.5-T0.5-Illum1_amp_gt.png) | ![](img/gray-RI1.5-T0.5-Illum1_phase_gt.png) | ![](img/gray-RI1.5-T0.5-Illum1_real_gt.png) | ![](img/gray-RI1.5-T0.5-Illum1_imag_gt.png) |                                             |
| BP   | ![](img/gray-RI1.5-T0.5-Illum1_amp_bp.png) | ![](img/gray-RI1.5-T0.5-Illum1_phase_bp.png) | ![](img/gray-RI1.5-T0.5-Illum1_real_bp.png) | ![](img/gray-RI1.5-T0.5-Illum1_imag_bp.png) |                                             |
| AD   | ![](img/gray-RI1.5-T0.5-Illum1_amp_ad.png) | ![](img/gray-RI1.5-T0.5-Illum1_phase_ad.png) | ![](img/gray-RI1.5-T0.5-Illum1_real_ad.png) | ![](img/gray-RI1.5-T0.5-Illum1_imag_ad.png) | ![](img/gray-RI1.5-T0.5-Illum1_loss_ad.png) |

- phase-only


|      |                    $A$                     |                    $\Phi$                    |                    $\Re$                    |                    $\Im$                    |                                             |
| ---- | :----------------------------------------: | :------------------------------------------: | :-----------------------------------------: | :-----------------------------------------: | ------------------------------------------- |
| GT   | ![](img/gray-RI1.5-T1.0-Illum1_amp_gt.png) | ![](img/gray-RI1.5-T1.0-Illum1_phase_gt.png) | ![](img/gray-RI1.5-T1.0-Illum1_real_gt.png) | ![](img/gray-RI1.5-T1.0-Illum1_imag_gt.png) |                                             |
| BP   | ![](img/gray-RI1.5-T1.0-Illum1_amp_bp.png) | ![](img/gray-RI1.5-T1.0-Illum1_phase_bp.png) | ![](img/gray-RI1.5-T1.0-Illum1_real_bp.png) | ![](img/gray-RI1.5-T1.0-Illum1_imag_bp.png) |                                             |
| AD   | ![](img/gray-RI1.5-T1.0-Illum1_amp_ad.png) | ![](img/gray-RI1.5-T1.0-Illum1_phase_ad.png) | ![](img/gray-RI1.5-T1.0-Illum1_real_ad.png) | ![](img/gray-RI1.5-T1.0-Illum1_imag_ad.png) | ![](img/gray-RI1.5-T1.0-Illum1_loss_ad.png) |

- amplitude-only


|      |                    $A$                     |                    $\Phi$                    |                    $\Re$                    |                    $\Im$                    |                                             |
| ---- | :----------------------------------------: | :------------------------------------------: | :-----------------------------------------: | :-----------------------------------------: | ------------------------------------------- |
| GT   | ![](img/gray-RI1.0-T0.0-Illum1_amp_gt.png) | ![](img/gray-RI1.0-T0.0-Illum1_phase_gt.png) | ![](img/gray-RI1.0-T0.0-Illum1_real_gt.png) | ![](img/gray-RI1.0-T0.0-Illum1_imag_gt.png) |                                             |
| BP   | ![](img/gray-RI1.0-T0.0-Illum1_amp_bp.png) | ![](img/gray-RI1.0-T0.0-Illum1_phase_bp.png) | ![](img/gray-RI1.0-T0.0-Illum1_real_bp.png) | ![](img/gray-RI1.0-T0.0-Illum1_imag_bp.png) |                                             |
| AD   | ![](img/gray-RI1.0-T0.0-Illum1_amp_ad.png) | ![](img/gray-RI1.0-T0.0-Illum1_phase_ad.png) | ![](img/gray-RI1.0-T0.0-Illum1_real_ad.png) | ![](img/gray-RI1.0-T0.0-Illum1_imag_ad.png) | ![](img/gray-RI1.0-T0.0-Illum1_loss_ad.png) |



### Continuous phase

- phase-only


|      |                    $A$                     |                    $\Phi$                    |                    $\Re$                    |                    $\Im$                    |                                             |
| ---- | :----------------------------------------: | :------------------------------------------: | :-----------------------------------------: | :-----------------------------------------: | ------------------------------------------- |
| GT   | ![](img/lens-RI1.5-T1.0-Illum1_amp_gt.png) | ![](img/lens-RI1.5-T1.0-Illum1_phase_gt.png) | ![](img/lens-RI1.5-T1.0-Illum1_real_gt.png) | ![](img/lens-RI1.5-T1.0-Illum1_imag_gt.png) |                                             |
| BP   | ![](img/lens-RI1.5-T1.0-Illum1_amp_bp.png) | ![](img/lens-RI1.5-T1.0-Illum1_phase_bp.png) | ![](img/lens-RI1.5-T1.0-Illum1_real_bp.png) | ![](img/lens-RI1.5-T1.0-Illum1_imag_bp.png) |                                             |
| AD   | ![](img/lens-RI1.5-T1.0-Illum1_amp_ad.png) | ![](img/lens-RI1.5-T1.0-Illum1_phase_ad.png) | ![](img/lens-RI1.5-T1.0-Illum1_real_ad.png) | ![](img/lens-RI1.5-T1.0-Illum1_imag_ad.png) | ![](img/lens-RI1.5-T1.0-Illum1_loss_ad.png) |

- complex


|      |                    $A$                     |                    $\Phi$                    |                    $\Re$                    |                    $\Im$                    |                                             |
| ---- | :----------------------------------------: | :------------------------------------------: | :-----------------------------------------: | :-----------------------------------------: | ------------------------------------------- |
| GT   | ![](img/lens-RI1.5-T0.5-Illum1_amp_gt.png) | ![](img/lens-RI1.5-T0.5-Illum1_phase_gt.png) | ![](img/lens-RI1.5-T0.5-Illum1_real_gt.png) | ![](img/lens-RI1.5-T0.5-Illum1_imag_gt.png) |                                             |
| BP   | ![](img/lens-RI1.5-T0.5-Illum1_amp_bp.png) | ![](img/lens-RI1.5-T0.5-Illum1_phase_bp.png) | ![](img/lens-RI1.5-T0.5-Illum1_real_bp.png) | ![](img/lens-RI1.5-T0.5-Illum1_imag_bp.png) |                                             |
| AD   | ![](img/lens-RI1.5-T0.5-Illum1_amp_ad.png) | ![](img/lens-RI1.5-T0.5-Illum1_phase_ad.png) | ![](img/lens-RI1.5-T0.5-Illum1_real_ad.png) | ![](img/lens-RI1.5-T0.5-Illum1_imag_ad.png) | ![](img/lens-RI1.5-T0.5-Illum1_loss_ad.png) |





-------

### Noise test

Reconstruction degrades with noise increasing, but 

- For positive, the acceptable noise db is: 25

- For negative, the  acceptable noise db is: 35



-------------------------------------------------------



## Experiments


|      |                  $A_{obj}$                  |                  $\phi_{obj}$                   |                     loss                      |
| :--: | :-----------------------------------------: | :---------------------------------------------: | :-------------------------------------------: |
|  BP  |   ![resthu_amp_bp](img/resthu_amp_bp.png)   |   ![resthu_phase_bp](img/resthu_phase_bp.png)   |      ![resthu_holo](img/resthu_holo.png)      |
|  AD  |   ![resthu_amp_ad](img/resthu_amp_ad.png)   |   ![resthu_phase_ad](img/resthu_phase_ad.png)   |   ![resthu_loss_ad](img/resthu_loss_ad.png)   |
|  BP  | ![particle_amp_bp](img/particle_amp_bp.png) | ![particle_phase_bp](img/particle_phase_bp.png) |    ![particle_holo](img/particle_holo.png)    |
|  AD  | ![particle_amp_ad](img/particle_amp_ad.png) | ![particle_phase_ad](img/particle_phase_ad.png) | ![particle_loss_ad](img/particle_loss_ad.png) |
|      |    ![cheek_amp_bp](img/cheek_amp_bp.png)    |    ![cheek_phase_bp](img/cheek_phase_bp.png)    |       ![cheek_holo](img/cheek_holo.png)       |
|      |    ![cheek_amp_ad](img/cheek_amp_ad.png)    |    ![cheek_phase_ad](img/cheek_phase_ad.png)    |    ![cheek_loss_ad](img/cheek_loss_ad.png)    |





### Negative illumination


|      |                 $A_{obj}$                 |                $\phi_{obj}$                 |                    loss                     |
| ---- | :---------------------------------------: | :-----------------------------------------: | :-----------------------------------------: |
| BP   |  ![circle_amp_bp](img/circle_amp_bp.png)  | ![circle_phase_bp](img/circle_phase_bp.png) |     ![circle_holo](img/circle_holo.png)     |
| AD   |  ![circle_amp_ad](img/circle_amp_ad.png)  | ![circle_phase_ad](img/circle_phase_ad.png) | ![circle_factor_ad](img/circle_loss_ad.png) |
|      |  ![random_amp_bp](img/random_amp_bp.png)  | ![random_phase_bp](img/random_phase_bp.png) |     ![random_holo](img/random_holo.png)     |
|      | ![random_phase_ad](img/random_amp_ad.png) | ![random_phase_ad](img/random_phase_ad.png) |  ![random_loss_ad](img/random_loss_ad.png)  |
|      |     ![res_amp_bp](img/res_amp_bp.png)     |    ![res_phase_bp](img/res_phase_bp.png)    |        ![res_holo](img/res_holo.png)        |
|      |     ![res_amp_ad](img/res_amp_ad.png)     |    ![res_phase_ad](img/res_phase_ad.png)    |     ![res_loss_ad](img/res_loss_ad.png)     |
|      |  ![simple_amp_bp](img/simple_amp_bp.png)  | ![simple_phase_bp](img/simple_phase_bp.png) |     ![simple_holo](img/simple_holo.png)     |
|      |  ![simple_amp_ad](img/simple_amp_ad.png)  | ![simple_phase_ad](img/simple_phase_ad.png) |  ![simple_loss_ad](img/simple_loss_ad.png)  |





### Experiments with GT

|                  Mask                   |              Phase              |
| :-------------------------------------: | :-----------------------------: |
| ![exp_amplitude](img/exp_amplitude.png) | ![exp_phase](img/exp_phase.png) |

- Since the etching may cause rough surface, it's better to let the chromium slide faces to the camera.
- tilted object produce a constant tilt phase delay



**Tilt model:**

|            Tilt plane wave induces a phase shift             |                       Math expression                        |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| <img src="img/tilt_illumination.png" alt="tilt_illumination" width=250px /> | $\phi_{tilt}=\frac{2\pi}{\lambda}[x \cos(\theta_x) + y \sin(\theta_z) ]\tan\theta_z$ |

$t_{obj} = \exp[j (\phi_{obj}  + \phi_{tilt})]$



|                    |                           no shift                           |                            shift                             |                          simulation                          |
| ------------------ | :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| $I_h$              | <img src="img/positive-16393827933511.bmp" alt="positive" height=180px /> | <img src="img/positive_shift-16393828017122.bmp" alt="positive_shift" height=180px /> | <img src="img/kaust_holo_abs_tilt(-20,0.5).png" alt="kaust_holo_abs_tilt(-20,0.5)" height=180px /> |
| $\mathcal{F}(I_h)$ | <img src="img/holo_spectrum.png" alt="holo_spectrum" height=180px /> | <img src="img/holo_spectrum_shift.png" alt="holo_spectrum_shift" height=180px /> | <img src="img/kaust_holo_spectrum_tilt(-20,0.5).png" alt="kaust_holo_spectrum_tilt(-20,0.5)" height=180px /> |

|            bg             |                    bg spectrum                     |                       holo substract                        |                       shift substract                        |
| :-----------------------: | :------------------------------------------------: | :---------------------------------------------------------: | :----------------------------------------------------------: |
|     ![bg](img/bg.bmp)     | ![bg_spectrum](img/bg_spectrum-16394002100905.png) | ![holo_spectrum_substract](img/holo_spectrum_substract.png) | ![holo_shift_spectrum_substract](img/holo_shift_spectrum_substract.png) |
| ![bg_new](img/bg_new.png) |    ![bg_spectrum_new](img/bg_spectrum_new.png)     |               ![ccd_glass](img/ccd_glass.jpg)               |                                                              |

Possible reasons:

- ~~system is not well aligned~~
- ~~CCD cover glass reflection/refraction (More likely)~~





------

$u _R = \exp[j 2 \pi(x f_{xR} + y f_{yR})]$
$$
I(r) = |u _O(r) | ^2 + | u _R(r) | ^2 + u _O(r) u _R ^*(r) + u _O ^*(r) u _R(r)
$$

$$
\begin{equation}
\begin{aligned}
\mathcal{F}(I) 
&= \mathcal{F}\lbrace |u _O(r) | ^2 + | u _R(r) | ^2 + u _O(r) u _R ^*(r) + u _O ^*(r) u _R(r) \rbrace    \\
&= \underbrace{u _O \otimes u _O^* +  u _R^2  \delta(w) }_\text{zero-order} + u _O \otimes u _R^* \delta(w - w_0)  + u _O^* \otimes u _R \delta(w + w_o)    \\
&= \underbrace{u _O \otimes u _O^* +  \delta(0,0) }_\text{zero-order} + U _O (f_x - f_{xR}, f_y - f_{yR})  + U _O^* (-f_x + f_{xR}, -f_y + f_{yR})   \\
\end{aligned}
\end{equation}
$$



----



|        |                                                 | Holograms                                 |
| ------ | ----------------------------------------------- | ----------------------------------------- |
| onaxis | ![exp_onaxis_effect](img/exp_onaxis_effect.gif) | ![positive](img/positive.bmp)             |
| tilted | ![exp_shift](img/exp_shift.jpg)                 | ![positive_shift](img/positive_shift.bmp) |



| wafer         | light band                | transmission | refractive |       |
| ------------- | ------------------------- | ------------ | ---------- | ----- |
| silica        |                           |              |            |       |
| fused  silica | visible and near infrared |              |            |       |
| silicon       |                           |              |            | cheap |
| Ge            | middle infrared           |              |            |       |
| optical glass |                           |              |            |       |
| sapphire      |                           |              |            |       |





-------

|      |                   positive amplitude mask                    |                                                              |
| ---- | :----------------------------------------------------------: | :----------------------------------------------------------: |
| Holo | ![kaust_positive_amp_holo](img/kaust_positive_amp_holo.png)  |                                                              |
|      |                             $A$                              |                            $\Phi$                            |
| BP   | ![kaust_positive_amp_amp_bp](img/kaust_positive_amp_amp_bp.png) | ![kaust_positive_amp_phase_bp](img/kaust_positive_amp_phase_bp.png) |
| AD   | ![kaust_positive_amp_amp_ad](img/kaust_positive_amp_amp_ad.png) | ![kaust_positive_amp_phase_ad](img/kaust_positive_amp_phase_ad.png) |



|      |                        positive phase                        |                                                              |
| ---- | :----------------------------------------------------------: | :----------------------------------------------------------: |
| Holo | ![kaust_positive_phase_holo](img/kaust_positive_phase_holo.png) |                                                              |
|      |                             $A$                              |                            $\Phi$                            |
| BP   |           ![](img/kaust_positive_phase_amp_bp.png)           |          ![](img/kaust_positive_phase_phase_bp.png)          |
| AD   | ![kaust_positive_phase_amp_ad](img/kaust_positive_phase_amp_ad.png) | ![kaust_positive_phase_phase_ad](img/kaust_positive_phase_phase_ad.png) |








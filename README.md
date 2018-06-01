# The C++ Project Readme #

This is the readme for the C++ project.


## The Tasks ##


### Body rate and roll/pitch control (scenario 2) ###


#### Implement body rate control

##### implement the code in the function [`GenerateMotorCommands()`](src/QuadControl.cpp#L58-L94)
  - induce the formula

##### implement the code in the function [`BodyRateControl()`](src/QuadControl.cpp#L96-L120)
  - The commanded roll, pitch, and yaw are collected by the body rate controller, and they are translated into the desired rotational accelerations along the axis in the body frame.
  - the fomula:
    - $p_{\text{error}} = p_c - p$
    - $\bar{u}_p= k_{p-p} p_{\text{error}}$
    - $q_{\text{error}} = q_c - q$
    - $\bar{u}_q= k_{p-q} q_{\text{error}}$
    - $r_{\text{error}} = r_c - r$
    - $\bar{u}_r= k_{p-r} r_{\text{error}}$

##### Tune `kpPQR` in `QuadControlParams.txt`
  - `kpPQR = 50, 50, 10`


#### Implement roll / pitch control

##### implement the code in the function [`RollPitchControl()`](src/QuadControl.cpp#L123-L171)

- The roll-pitch controller is a P controller responsible for commanding the roll and pitch rates ($p_c$ and $q_c$) in the body frame. It sets the desired rate of change of the given matrix elements using a P controller.

- $$
\begin{pmatrix} p_c \\ q_c \\ \end{pmatrix}  = \frac{1}{R_{33}}\begin{pmatrix} R_{21} & -R_{11} \\ R_{22} & -R_{12} \end{pmatrix} \times \begin{pmatrix} \dot{b}^x_c \\ \dot{b}^y_c  \end{pmatrix}
$$
##### Tune `kpBank` in `QuadControlParams.txt`
- kpBank = 15


<p align="center">
<img src="images/scenario2.png" width="700"/>
</p>


### Position/velocity and yaw angle control (scenario 3) ###


##### implement the code in the function [`LateralPositionControl()`](src/QuadControl.cpp#L214-L261)
  - The lateral controller will use a PD controller to command target values for elements of the drone's rotation matrix. The drone generates lateral acceleration by changing the body orientation which results in non-zero thrust in the desired direction. This will translate into the commanded rotation matrix elements $b^x_c$ and $b^y_c$.
  - $$
\begin{align}
\ddot{x}_{\text{command}} &=  c b^x_c \\
\ddot{x}_{\text{command}} &=  k^x_p(x_t-x_a) + k_d^x(\dot{x}_t - \dot{x}_a)+ \ddot{x}_t \\
b^x_c &= \ddot{x}_{\text{command}}/c
\end{align}
$$

##### implement the code in the function [`AltitudeControl()`](src/QuadControl.cpp#L173-L211)

##### tune parameters `kpPosXY` and `kpPosZ`
- `kpPosXY = 3`
- `kpPosZ = 4`

##### tune parameters `kpVelXY` and `kpVelZ`
- `kpVelXY = 10`
- `kpVelZ = 11`

##### implement the code in the function [`YawControl()`](src/QuadControl.cpp#L264-L288)
  - Control over yaw is decoupled from the other directions. A P controller is used to control the drone's yaw.
  - $r_c = k_p (\psi_t - \psi_a)$

##### tune parameters `kpYaw` and the 3rd (z) component of `kpPQR`
  - `kpYaw = 2`
  - `kpPQR = 50, 50, 10`



<p align="center">
<img src="images/scenario3.png" width="700"/>
</p>

**Hint:**  For a second order system, such as the one for this quadcopter, the velocity gain (`kpVelXY` and `kpVelZ`) should be at least ~3-4 times greater than the respective position gain (`kpPosXY` and `kpPosZ`).

### Non-idealities and robustness (scenario 4) ###

In this part, we will explore some of the non-idealities and robustness of a controller.  For this simulation, we will use `Scenario 4`.  This is a configuration with 3 quads that are all are trying to move one meter forward.  However, this time, these quads are all a bit different:
 - The green quad has its center of mass shifted back
 - The orange vehicle is an ideal quad
 - The red vehicle is heavier than usual


1. Edit `AltitudeControl()` to add basic integral control to help with the different-mass vehicle.

2. Tune the integral control, and other control parameters until all the quads successfully move properly.  

<p align="center">
<img src="images/scenario4.png" width="700"/>
</p>

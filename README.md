# UAV-Control-Physics-Informed-Machine-Learning

Integrating Dynamic Mode Decomposition (DMD) control and Physics-Informed Neural Networks (PINNs) to enhance UAV quadcopter control systems represents a novel approach. By combining DMD techniques with PINNs to solve the Riccati equation, this method is crucial for accurate UAV position estimation. It formulates the UAV control problem using physics-based constraints, ensuring that the learned models adhere to the physical laws governing UAV dynamics. DMD extracts fundamental dynamic modes from the data using a comprehensive dataset with UAV flight information such as position, velocity, and control inputs. These modes provide a reduced-order representation that captures the dominant UAV dynamics. This representation is then used within the PINN framework to solve the Riccati equation accurately. Integrating DMD and PINNs creates a powerful control strategy, enhancing position estimation accuracy and optimizing control performance. Experimental results validate the effectiveness of this method in real-time UAV control scenarios, demonstrating significant improvements in estimation accuracy and control stability compared to traditional approaches.

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/PINN_M.jpg)

The process of integrating DMD with PINN for UAV position and orientation estimation is as follows: The sequence illustrates the training process, including the loss function and neural network architecture.

![](https://github.com/1Px-Vision/UAV-Control-Physics-Informed-Machine-Learning/blob/main/SImulation_PINN.jpg)

Reference drone trajectory used for evaluating the performance of DMD and PINN control methods.

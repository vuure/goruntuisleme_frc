
package frc.robot;

import edu.wpi.first.math.controller.PIDController;
import edu.wpi.first.math.geometry.Rotation2d;
import edu.wpi.first.math.geometry.Transform3d;
import edu.wpi.first.wpilibj.ADXRS450_Gyro;
import edu.wpi.first.wpilibj.Joystick;
import edu.wpi.first.wpilibj.TimedRobot;
import edu.wpi.first.wpilibj.drive.MecanumDrive;
import edu.wpi.first.wpilibj.motorcontrol.PWMSparkMax;
import org.photonvision.PhotonCamera;
import org.photonvision.PhotonUtils;
import org.photonvision.targeting.PhotonTrackedTarget;

public class Robot extends TimedRobot {

  ADXRS450_Gyro gyro = new ADXRS450_Gyro();

  Joystick controller = new Joystick(0);

  double leftYaxis;
  double rightXaxis;
  double leftXaxis;
  Rotation2d gyroRotation2D;

  private final PWMSparkMax frontleft = new PWMSparkMax(0);
  private final PWMSparkMax frontright = new PWMSparkMax(1);
  private final PWMSparkMax backleft = new PWMSparkMax(2);
  private final PWMSparkMax backright = new PWMSparkMax(3);

  MecanumDrive m_robotDrive = new MecanumDrive(frontleft, backleft, frontright, backright);

  PhotonCamera cam = new PhotonCamera("photonvision");

  @Override
  public void teleopPeriodic() {

    PIDController turnController = new PIDController(0.1, 0, 0.1);

    var result = cam.getLatestResult();

    leftYaxis = controller.getRawAxis(1);
    rightXaxis = controller.getRawAxis(0);
    leftXaxis = controller.getRawAxis(4);

    gyroRotation2D = gyro.getRotation2d();

    if (result.hasTargets()){

      PhotonTrackedTarget target = result.getBestTarget();
      double robotYaw = target.getYaw();

      m_robotDrive.driveCartesian(-leftYaxis, -rightXaxis, turnController.calculate(robotYaw, 0), gyroRotation2D);

    } else {

      m_robotDrive.driveCartesian(-leftYaxis, -rightXaxis, leftXaxis, gyroRotation2D);

    }
  }
}

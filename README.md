# STM32F407 + micro-ROS over USB

I got micro-ROS running on an STM32F407 and managed to get it talking to ROS 2 Humble on Ubuntu over USB CDC. Putting this here so I don't forget what I did.

## Setup

**On the Ubuntu side**, I installed ROS 2 Humble and the micro-ROS agent:
```bash
sudo apt install ros-humble-micro-ros-agent
```

Also had to add myself to the `dialout` group to avoid permission issues with `/dev/ttyACM0`:
```bash
sudo usermod -aG dialout $USER
```

**On the STM32 side**, I used STM32CubeMX to configure USB as CDC (Device Only) and pulled in the micro-ROS static library from `micro_ros_stm32cubemx_utils`. Clock is set to 168 MHz with 8 MHz HSE, which is the standard for the Discovery board.

## Running it

1. Plug in the board
2. Start the agent:
```bash
source /opt/ros/humble/setup.bash
ros2 run micro_ros_agent micro_ros_agent serial --dev /dev/ttyACM0 -b 115200
```
3. Reset the board — after that the agent should pick it up
4. Check with `ros2 topic echo /stm32_topic`

## Notes to myself

- The board has to be reset **after** the agent is already running, otherwise it won't connect
- If `/dev/ttyACM0` doesn't show up, `dmesg | grep tty` usually tells me what's going on
- `ROS_DOMAIN_ID` needs to match on both sides (I just leave it at 0)

So whenever you want to connect to your robot with your ros host you have to connect to it using its domain.

Every time you start both, if you're not using the default of 30, you'll have to retype the value in.

```
export ROS_DOMAIN_ID="your-number"
```

So to avoid this you'll want to go ahead and set your pi and host to auto run this when they're starting up so that they're on the same page from the moment that you turn them on.

You want to do this with any of the export commands

https://emanual.robotis.com/docs/en/platform/turtlebot3/bringup/#bringup

A similar thing could be done with the bring up services for the pi. You could have them started up on the start up of the device or, you could us `tmux` and run the command in a different instance of `tmux` so that it runs in the background once you've run it.

`sudo apt install tmux` (if it's not already installed)

Then just run `ros2 launch turtlebot3_bringup robot.launch.py` in there.

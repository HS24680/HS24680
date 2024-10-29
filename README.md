from dronekit import connect, VehicleMode, LocationGlobalRelative
import time

# Connect to the drone (use your drone's connection address here)
vehicle = connect('127.0.0.1:14550', wait_ready=True)

# Function to arm the drone and take off to a given altitude
def arm_and_takeoff(altitude):
    print("Arming motors and taking off...")
    vehicle.mode = VehicleMode("GUIDED")
    vehicle.armed = True
    
    while not vehicle.armed:
        print("Waiting for arming...")
        time.sleep(1)
    
    vehicle.simple_takeoff(altitude)
    
    # Wait until the drone reaches the target altitude
    while True:
        print("Altitude: ", vehicle.location.global_relative_frame.alt)
        if vehicle.location.global_relative_frame.alt >= altitude * 0.95:
            print("Reached target altitude")
            break
        time.sleep(1)

# Waypoints to navigate to, with latitude, longitude, and altitude
waypoints = [
    LocationGlobalRelative(-35.362938, 149.165085, 10),
    LocationGlobalRelative(-35.362500, 149.165200, 10),
    LocationGlobalRelative(-35.362200, 149.165300, 10),
]

# Simulating seed planting at each waypoint
def plant_seeds():
    for wp in waypoints:
        print(f"Flying to waypoint: {wp}")
        vehicle.simple_goto(wp)
        time.sleep(5)  # Give time for drone to reach the waypoint
        
        print("Simulating seed planting...")
        time.sleep(2)  # Simulate planting process

# Land the drone after finishing
def land():
    print("Landing the drone")
    vehicle.mode = VehicleMode("LAND")
    while vehicle.armed:
        print("Waiting for disarm...")
        time.sleep(1)
    print("Drone landed and disarmed.")

# Main sequence
arm_and_takeoff(10)  # Take off to 10 meters
plant_seeds()        # Visit each waypoint and "plant"
land()               # Land the drone

HOST: https://portal.vn.teslamotors.com

# Tesla Model S REST API
This is unofficial documentation of the Tesla Model S REST API used by the iOS and Android apps. It features functionality to monitor and control the Model S remotely.

# Authentication Flow
These endpoints handle login and session management

## GET /login
Returns the login form. Sets a *_s_portal_session* cookie for the session

### Response 200

#### Headers

	Set-Cookie: _s_portal_session={cookie}; path=/; secure; HttpOnly

#### Body

	{}

## POST /login
Performs the login. Takes in an plain text email and password, matching the owner's login from http://teslamotors.com/mytesla.
Sets a *user_credentials* cookie that expires in 3 months, which is passed along with all future requests to authenticate the user.
302 redirects back to a dummy welcome page. This page is ignored by the smartphone app and can be ignored by your API client.

### Request (application/x-www-form-urlencoded)

	user_session%5Bemail%5D=string&user_session%5Bpassword%5D=string

### Response 302

#### Headers

	Set-Cookie: _s_portal_session={cookie}; path=/; secure; HttpOnly
	Set-Cookie: user_credentials=x; path=/; expires=Fri, 03-May-2013 03:01:54 GMT; secure; HttpOnly
	Location: https://portal.vn.teslamotors.com/

#### Body

	{}

# Vehicle List
A logged in user can have multiple vehicles under their account. This resource is primarily responsible for listing the vehicles and the basic details about them.

Must have a *_s_portal_session* and *user_credentials* cookie set for all requests.

## GET /vehicles
Retrieve a list of your owned vehicles (includes vehicles not yet shipped!)

### Response 200 (application/json)

	[{
	    "color": null,
	    "display_name": null,
	    "id": 321,
	    "option_codes": "MS01,RENA,TM00,DRLH,PF00,BT85,PBCW,RFPO,WT19,IBMB,IDPB,TR00,SU01,SC01,TP01,AU01,CH00,HP00,PA00,PS00,AD02,X020,X025,X001,X003,X007,X011,X013",
	    "user_id": 123,
	    "vehicle_id": 1234567890,
	    "vin": "5YJSA1CN5CFP01657",
	    "tokens": ["x", "x"],
	    "state": "online"
	}]

# Vehicle Status
These resources are read-only and determine the state of the vehicle's various sub-systems.

Must have a *_s_portal_session* and *user_credentials* cookie set for all requests.

## GET /vehicles/{id}/mobile_enabled
Determines if mobile access to the vehicle is enabled.

### Response 200 (application/json)

	{
	    "reason":"",
	    "result":true
	}

## GET /vehicles/{id}/command/charge_state
Returns the state of charge in the battery.

### Response 200 (application/json)

#### Body

+ charging_state (string) ... "Charging", ??
+ charge_to_max_range (bool) ... current std/max-range setting
+ max_range_charge_counter (number)
+ fast_charger_present (bool) ... connected to Supercharger?
+ battery_range (number) ... rated miles
+ est_battery_range (number) ... range estimated from recent driving
+ ideal_battery_range (number) ... ideal miles
+ battery_level (number) ... integer charge percentage
+ battery_current (number) ... current flowing into battery
+ charger_voltage (number) ... only has value while charging
+ charger_pilot_current ... max current allowed by charger & adapter
+ charger_actual_current (number) ... current actually being drawn
+ charger_power (number) ... kW (rounded down) of charger
+ time_to_full_charge (number) ... valid only while charging
+ charge_rate (number) ... float mi/hr charging or -1 if not charging
+ charge_port_door_open (bool)

	{
	    "charging_state": "Complete",
	    "charge_to_max_range": false,
	    "max_range_charge_counter": 0,
	    "fast_charger_present": false,
	    "battery_range": 239.02,
	    "est_battery_range": 155.79,
	    "ideal_battery_range": 275.09,
	    "battery_level": 91,
	    "battery_current": -0.6,
	    "charge_starting_range": null,
	    "charge_starting_soc": null,
	    "charger_voltage": 0,
	    "charger_pilot_current": 40,
	    "charger_actual_current": 0,
	    "charger_power": 0,
	    "time_to_full_charge": null,
	    "charge_rate": -1.0,
	    "charge_port_door_open": true
	}

## GET /vehicles/{id}/command/climate_state
Returns the current temperature and climate control state.

### Response 200 (application/json)

#### Body

+ inside_temp (number) ... degC inside car
+ outside_temp (number) ... degC outside car or null
+ driver_temp_setting (number) ... degC of driver temperature setpoint
+ passenger_temp_setting (number) ... degC of passenger temperature setpoint
+ is_auto_conditioning_on (bool) ... apparently even if on
+ is_front_defroster_on ... null or boolean as integer?
+ is_rear_defroster_on
+ fan_status ... fan speed 0-6 or null

	{
	    "inside_temp": 17.0,
	    "outside_temp": 9.5,
	    "driver_temp_setting": 22.6,
	    "passenger_temp_setting": 22.6,
	    "is_auto_conditioning_on": false,
	    "is_front_defroster_on": null,
	    "is_rear_defroster_on": false,
	    "fan_status": 0
	}


## GET /vehicles/{id}/command/drive_state
Returns the driving and position state of the vehicle.

### Response 200 (application/json)

#### Body

+ shift_state
+ speed
+ latitude (number) ... degrees N of equator
+ longitude (number) ... degrees W of the prime meridian
+ heading (number) ... integer compass heading, 0-359
+ gps_as_of (number) ... Unix timestamp of GPS fix

	{
	    "shift_state": null,
	    "speed": null,
	    "latitude": 33.794839,
	    "longitude": -84.401593,
	    "heading": 4,
	    "gps_as_of": 1359863204
	}

## GET /vehicles/{id}/command/gui_settings
Returns various information about the GUI settings of the car, such as unit format and range display.

### Response 200 (application/json)
	{
	    "gui_distance_units": "mi/hr",
	    "gui_temperature_units": "F",
	    "gui_charge_rate_units": "mi/hr",
	    "gui_24_hour_time": false,
	    "gui_range_display": "Rated"
	}


## GET /vehicles/{id}/command/vehicle_state
Returns the vehicle's physical state, such as which doors are open.

### Response 200 (application/json)

#### Body

+ df (bool) ... driver's side front door open
+ dr (bool) ... driver's side rear door open
+ pf (bool) ... passenger's side front door open
+ pr (bool) ... passenger's side rear door open
+ ft (bool) ... front trunk is open
+ rt (bool) ... rear trunk is open
+ car_verson (string) ... car firmware version
+ locked (bool) ... car is locked
+ sun_roof_installed (bool) ... panoramic roof is installed
+ sun_roof_state (string)
+ sun_roof_percent_open (number) ... null if not installed
+ dark_rims (bool) ... gray rims installed
+ wheel_type (string) ... wheel type installed
+ has_spoiler (bool) ... spoiler is installed
+ roof_color ... "None" for panoramic roof

	{
	    "df": false,
	    "dr": false,
	    "pf": false,
	    "pr": false,
	    "ft": false,
	    "rt": false,
	    "car_verson": "1.19.42",
	    "locked": true,
	    "sun_roof_installed": false,
	    "sun_roof_state": "unknown",
	    "sun_roof_percent_open": 0,
	    "dark_rims": false,
	    "wheel_type": "Base19",
	    "has_spoiler": false,
	    "roof_color": "Colored",
	    "perf_config": "Base"
	}

# Vehicle Commands


## GET /vehicles/{id}/command/charge_port_door_open
Open the charge port

### Response 200 (application/json)

	{
	  "result": false,
	  "reason": "failure reason"
	}

## GET /vehicles/{id}/command/charge_standard
Set the charge mode to standard.

### Response 200 (application/json)

	{
	  "result": false,
	  "reason": "failure reason"
	}

## GET /vehicles/{id}/command/charge_max_range
Set the charge mode to max-range.

### Response 200 (application/json)
	{
	  "result": false,
	  "reason": "failure reason"
	}

## GET /vehicles/{id}/command/charge_start
Start charging

### Response 200 (application/json)

	{
	  "result": false,
	  "reason": "failure reason"  // "already started" if a charge is in progress
	}

## GET /vehicles/{id}/command/charge_stop
Stop charging

### Response 200 (application/json)

	{
	  "result": false,
	  "reason": "failure reason"   // "not_charging" if a charge was not in progress
	}

## GET /vehicles/{id}/command/flash_lights
Flash the lights once

### Response 200 (application/json)

	{
	  "result": false,
	  "reason": "failure reason"
	}

## GET /vehicles/{id}/command/honk_horn
Honk horn once

### Response 200 (application/json)

	{
	  "result": false,
	  "reason": "failure reason"
	}

## GET /vehicles/{id}/command/door_unlock
Unlock doors

### Response 200 (application/json)

	{
	  "result": false,
	  "reason": "failure reason"
	}

## GET /vehicles/{id}/command/door_lock
Lock doors

### Response 200 (application/json)

	{
	  "result": false,
	  "reason": "failure reason"
	}

## GET /vehicles/{id}/command/set_temps?driver_temp={driver_degC}&passenger_temp={pass_degC}
Set the temperature setpoints

### Response 200 (application/json)

	{
	  "result": false,
	  "reason": "failure reason"
	}

## GET /vehicles/{id}/command/auto_conditioning_start
Start HVAC

### Response 200 (application/json)

	{
	  "result": false,
	  "reason": "failure reason"
	}

## GET /vehicles/{id}/command/auto_conditioning_stop
Stop HVAC

### Response 200 (application/json)

	{
	  "result": false,
	  "reason": "failure reason"
	}

## GET /vehicles/{id}/command/sun_roof_control?state={state}
Panoramic roof control

### Parameters
+ state (string) ... State may be "open", "close", "comfort", or "vent".

### Response 200 (application/json)
	{
	  "result": false,
	  "reason": "failure reason"
	}

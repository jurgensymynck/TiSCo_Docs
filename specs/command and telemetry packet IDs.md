20/05/2024, v0.9


# Command value overview

## CMD_ECHO and CMD_TEST: 0x0[0...F] and some exeptions

For testing connection
* CMD_NONE					= 0x00;
* CMD_PING					= 0x01;
* CMD_ECHO_UINT32			= 0x0E;
* CMD_ECHO_UINT16_ARRAY		= 0x7E;

For testing sync pattern
* CMD_TEST_55				= 0x55;
* CMD_TEST_D5				= 0xD5;
* CMD_TEST_5D				= 0x5D;

For testing bit endianess and other communication
* CMD_TEST_0F				= 0x0F;
* CMD_TEST_F0				= 0xF0;
* CMD_TEST_FF				= 0xFF;


## SET commands
* CMD_SET_JOINT_PWMPULSE							    = 0x11;
    * joint number and single 16 bit value
* CMD_SET_JOINT_SLEEP_ALL								= 0x12;
* CMD_SET_JOINT_WAKEUP_ALL								= 0x13;
* CMD_SET_JOINT_PWMPULSE_ALL					        = 0x14;
    * array of NUM
* CMD_SET_JOINT_STEP									= 0x15;
    * joint number and 8 bit value
* CMD_SET_JOINT_LABEL									= 0x16;
    * joint number and string
* CMD_ATTACH_DETACH_TLM_AND_SAMPLE_INTERRUPT			= 0x17;
    * attach or detach
* CMD_SET_MOTION_INTERRUPT								= 0x18;
    * REWORK THIS!
* CMD_SET_INPUT_TO_ANALOG								= 0x30;
    * boolean
* CMD_SET_ROBOTDIGITALOUTPUTS							= 0x40;
    32 bit value
* CMD_SET_MESSAGE_MASK									= 0x60;
    32 bit value
* CMD_SET_RATE_TLM_AND_SAMPLE							= 0x61;
* CMD_SET_PWM_AND_MOTION								= 0x62;


* CMD_FETCH_JOINTPWMPULSE								= 0x21;
* CMD_FETCH_JOINTANGLE_RAW								= 0x22;
* CMD_FETCH_JOINTANGLE_MAPPED_TO_PWMPULSE				= 0x23;
* CMD_FETCH_JOINTINPUT_RAW								= 0x25;
* CMD_FETCH_JOINTSTATE									= 0x28;
* CMD_FETCH_JOINTLABEL									= 0x29;
* CMD_FETCH_ROBOTDIGITALINPUTS							= 0x41;
* CMD_FETCH_ROBOTDIGITALOUTPUTS							= 0x42;
* CMD_FETCH_MESSAGE_MASK								= 0x43;

* TLM_PING												= 0x81;
* TLM_ECHO_UINT32										= 0x8E;
* TLM_ECHO_UINT16_ARRAY									= 0xFE;
* TLM_JOINTPWMPULSE										= 0xA1;
* TLM_JOINTANGLE_RAW									= 0xA2;
* TLM_JOINTANGLE_MAPPED_TO_PWMPULSE						= 0xA3;
* TLM_JOINTCURRENT_RAW									= 0xA4;
* TLM_JOINTINPUT_RAW									= 0xA5;
* TLM_YYY												= 0xA6;
* TLM_ZZZ												= 0xA7;
* TLM_JOINTSTATE										= 0xA8;
* TLM_JOINTLABEL										= 0xA9;

* TLM_ROBOTDIGITALINPUTS								= 0xC1;
* TLM_ROBOTDIGITALOUTPUTS								= 0xC2;
* TLM_MESSAGE_MASK										= 0xC3;

* CMD_ROBOT_MOTION_START_STOP								= 0x63;
* CMD_ROBOT_MOTION_STOP									= 0x64;
* CMD_ROBOT_MOTION_PAUSE_RESTART						= 0x65;


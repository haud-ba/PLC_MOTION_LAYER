# Introduction 
## **PLC_OPEN_DRIVE** (deprecated)

## **PLC_MOTION** (active)
  
  - TwinCAT motion layer approach
	- Manual compiler defines for optional binding of libraries ('library'RMB:Properties:Conditional Referencing)
      - NCI
	  - CAM
	- Manual compiler define for file systems
	  - BSD
    - struct/state based communication to extern control
      - any communication layer, choose signal type in TwinCAT
      - axis state carries selected information in order to keep mapping small
    - MC PtP lib; Conditions: 'empty'
      - Tc2_MC2
      - Tc2_MC2_Drive
      - Tc2_NC
    - NCI lib; Conditions: NCI
      - Tc2_NCI
      - Tc2_PlcInterpolation
    - CAMMING lib; Conditions: CAM
      - Tc2_MC2_Camming
    - for each of the functions there is a Ctrl/State pair of structs and additional parameter structs to write/read
    - error logging in MsgList, info logging optional, automated write to file optional
    - enum based commands and orders
      - E_AXIS_CTRL 		--> Ptp
      - E_NCI_CTRL 			--> NCI (optional)
      - E_AXIS_CTRL 		--> Camming (optional)
      
## NAMESPACES [_GVL]
	- GVL_AXIS	- MC/PtP
	- GVL_NCI	- interpolated
	- GVL_CAM	- Camming
	- GVL_MSG
	
 
    




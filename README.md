# Introduction 
## **PLC_OPEN_DRIVE**
  
  - TwinCAT motion layer approach
    - struct/state based communication to extern control
      - any communication layer, choose signal type in TwinCAT
      - axis state carries selected information in order to keep mapping small
    - MC PtP lib
      - Tc2_MC2
      - Tc2_MC2_Drive
      - Tc2_NC
    - NCI lib
      - Tc2_NCI
      - Tc2_PlcInterpolation
    - CAMMING lib
      - Tc2_MC2_Camming
    - for each of the functions there is a Ctrl/State pair of structs and additional parameter structs to write/read
    - error logging in MsgList, info logging optional, automated write to file optional
    - enum based commands and orders
      - e_AxisFunction --> Ptp
      - e_Ctrl_NciCommand --> NCI
      - CAMMING V00.50.1 - still under construction
      
      ## DOC
          //---------------------------------------------------------------------------------------------------------------
          //
          //  HAUD 2023.12.23
          //
          //
          //
          //  Documentation TC_Dos_Nci/PLC_OPEN_DRIVE
          //
          //  - MC and NCI encapsulation for outside use
          //  - ADS and cyclic access possible
          //  - cyclic interface through TwinCAT mapping (pragmas)
          //  - use of compiler defines for software options
          //
          //-------------------------------------------------------------
          //  - cyclic interface to NC
          //-------------------------------------------------------------
          //    - GVL_AXIS_CTRL.gfbAxisController[].fbAxis.pAxisRef
          //    - GVL_NCI.gfbNciCtrl[1]._fbNciChannel._refNciChannelRef
          //
          //-------------------------------------------------------------
          //  - PTP interface to outside world
          //-------------------------------------------------------------
          //    - GVL_AXIS_CTRL.gstAxisControl - %Q* and %I* cyclic data
          //    - GVL_AXIS_CTRL.gstAxisControl : ST_AxisController
          //
          //  TYPE ST_AxisController :
          //  STRUCT
          //
          //  {IF defined (AXIS_MAP)}
          //    {info 'mapped control'}
          //    ToPlc    AT %I* : ST_In;
          //    FromPlc  AT %Q* : ST_Out;
          //  {ELSE}
          //    {info 'local control'}
          //    ToPlc    : ST_In;
          //    FromPlc  : ST_Out;
          //  {END_IF}
          //
          //  END_STRUCT
          //  END_TYPE
          //
          //
          //  {attribute 'pack_mode' := '2'}
          //  TYPE ST_In :
          //  STRUCT
          //    bFastStop         : BIT;  // Fast stop function of NC with mapped 
          //                                 input
          //    byAxisFuncPara    : BYTE; // additional parameter for 
          //                                 Axis Function; 
          //                                 e.g. used for AxisIndex on GearIn
          //    uiAxisFunctionReq : e_AxisFunction; // selected function for axis 
          //    rSetPos           : LREAL;
          //    rSetVelo          : LREAL;
          //    rOverride         : LREAL;
          //    rAcc              : LREAL;
          //    rDec              : LREAL;
          //    iDirection        : INT;
          //  END_STRUCT
          //  END_TYPE
          //
          //
          //  TYPE e_AxisFunction :
          //  (
          //    AXIS_DISABLE,
          //    AXIS_IDLE := 1,
          //    AXIS_ENABLE := 10,
          //    AXIS_HOME,
          //    AXIS_MOVE_POS,
          //    AXIS_MOVE_VELO,
          //    AXIS_RESET,
          //    AXIS_HALT,
          //    AXIS_STOP,
          //
          //    AXIS_MOVE_BUFFER := 20,
          //    AXIS_MOVE_BUFFER_VELO,
          //    AXIS_WRITE_BUFFER_MODE,
          //
          //    AXIS_PARAMETER   := 30, // axis function for reading and 
          //                               writing parameter
          //
          //
          //    AXIS_GEAR_IN    := 50,
          //    AXIS_GEAR_OUT,
          //    AXIS_GEAR_IN_MULTIMASTER,
          //    
          //    AXIS_SS1_STOP   :=  99,
          //    AXIS_BUSY       := 100,
          //    AXIS_STARTUP    := 200,
          //    AXIS_DONE       := 1000,
          //
          //    ERROR           := 9000
          //  )UINT;
          //  END_TYPE
          //
          //
          //
          //-------------------------------------------------------------
          //  - NCI interface to outside world
          //-------------------------------------------------------------
          //
          //  - cyclic ctrl interface for starting NCI functions
          //      GVL_NCI.gstChannelCtrl  - command struct
          //      GVL_NCI.gstChannelState - status struct
          //
          //  - config structs required for jobs
          //      GVL_NCI.gstAxisConfig   - NC Axis Id for building Group
          //      GVL_NCI.gstAxisIndex    - Index in 
          //        GVL_AXIS_CTRL.gfbAxisController[].fbAxis.pAxisRef
          //
          //      GVL_NCI.gsProgName      - Commanded name for *.nc file
          //      GVL_NCI.gsProgNameActive- active or last *.nc file
          //
          //      GVL_NCI.gstRParameter   - array of lreals to use on 
          //        R Parameter Range (max 10 consecutive entries)
          //
          //  - available commands:
          //
          //  {attribute 'qualified_only'}
          //  {attribute 'to_string'}
          //  TYPE e_Ctrl_NciCommand :
          //  (
          //    NO_NCI            := -10,
          //    NO_AXIS_GROUP     := -2,
          //    NO_AXIS_INDEX     := -1,
          //    NO_INIT,
          //    INIT,
          //    IDLE,
          //    RESET,
          //    LOAD_PROG,
          //    FEED_TABLE,
          //    BUILD_GROUP,
          //    CLEAR_GROUP,
          //
          //    START         := 10,
          //    STOP,
          //    STO_STOP,
          //    RESTART_STEP_ON,
          //
          //    M_FUNC_QUIT   := 20,
          //
          //    READ_R_PARA   := 30,
          //    WRITE_R_PARA  := 40,
          //
          //    CMD_BUSY      := 100,
          //    CMD_DONE      := 1000,
          //    CMD_ERROR     := 9000
          //  )DINT;
          //  END_TYPE
          //
          //
          //---------------------------------------------------------------------------------------------------------------
          // This SOFTWARE is provided as an Exemple by THE PROVIDER "as is" 
          // and "with all faults." THE PROVIDER makes no 
          // representations or warranties of any kind concerning the safety, suitability, 
          // lack of viruses, inaccuracies, 
          // typographical errors, or other harmful components of this SOFTWARE. 
          // There are inherent dangers in the use of 
          // any software, and you are solely responsible for determining whether 
          // this SOFTWARE is compatible with your 
          // equipment and other software installed on your equipment. 
          // You are also solely responsible for the protection 
          // of your equipment and backup of your data, and THE PROVIDER will not be liable 
          // for any damages you may suffer 
          // in connection with using, modifying, or distributing this SOFTWARE.
          //---------------------------------------------------------------------------------------------------------------

      
    




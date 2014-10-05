LibGDX can be combined with [Intel RealSense](https://software.intel.com/en-us/realsense/home). RealSense is a suite of software (algorithms plus data files) and hardware (depth/color camera with mirophone) that let’s you explore human computer interaction, such as hand and face tracking or gesture and voice recognition.

## Requirements

Before you can start using RealSense with libGDX you have to [register with Intel](https://software.intel.com/partner/app/registration/developer?locale=en-us). Once registered, you can proceed to [download the RealSense SDK] (search for the string 'Download the SDK' on that page).

The RealSense SDK only supports Windows 8+ and requires a [supported camera](https://software.intel.com/en-us/realsense/integrated-camera-and-supported-systems).

## Setting up your libGDX RealSense project]

## Distributing your libGDX RealSense project]
The RealSense SDK comes with a set of runtime installers in the `runtime` directory. You have to distribute the `intel_rs_sdk_runtime_<version>.exe` file to your users and have them install the runtime.

Once installed, you end users can simply open your libGDX desktop application.
---
title: buildTX2KernelForZR300
date: 2018-01-11 10:18:34
tags: [tx2,realsense]
categories: Trick
---

---
# **IMPORTANT**
# R28.1(Jetson Pack 3.1) Not Compatible with *ZR300* 1.12.1 !!!
# GIVE IT UP!! DONT WASTE YOUR TIME ANYMORE !!
---

# buildJetsonTX2Kernel For ZR300
Scripts to help build the 4.4.38 kernel and modules onboard the Jetson TX2 (L4T 28.1, JetPack 3.1). For previous versions, visit the 'tags' section.

As of this writing, the "official" way to build the Jetson TX2 kernel is to use a cross compiler on a Linux PC. This is an alternative which builds the kernel onboard the Jetson itself. These scripts will download the kernel source to the Jetson TX2, wrangle some of the Makefiles to make them work on the Jetson, and then compile the kernel and selected modules. The newly compiled kernel can then be installed.

These scripts are for building the kernel for the 64-bit L4T 28.1 (Ubuntu 16.04 based) operating system on the NVIDIA Jetson TX2. The scripts should be run directly after flashing the Jetson with L4T 28.1 from a host PC. There are three scripts:

<strong>getKernelSources.sh</strong>

Downloads the kernel sources for L4T 28.1 from the NVIDIA website, decompresses them and opens a graphical editor on the .config file. In editor, select General Setup/Local Version - append to kernel release, and change name of the kernel to match you kenel name (e.g -jetsonTX). 

To add support of INZI pixel format for realsense SR300, follow the patch by realsense from https://www.spinics.net/lists/linux-media/msg108702.html and add following lines if not present:

usr/src/kernel/kernel-4.4/drivers/media/usb/uvc/uvcvideo.h in GUIDs macros:

	#define UVC_GUID_FORMAT_RAW8 \
		{ 'R',  'A',  'W',  '8', 0x66, 0x1a, 0x42, 0xa2, \
		0x90, 0x65, 0xd0, 0x18, 0x14, 0xa8, 0xef, 0x8a}
	#define UVC_GUID_FORMAT_RW16 \
		{ 'R',  'W',  '1',  '6', 0x00, 0x00, 0x10, 0x00, \
		0x80, 0x00, 0x00, 0xaa, 0x00, 0x38, 0x9b, 0x71}
	#define UVC_GUID_FORMAT_INVZ \
		{ 'I',  'N',  'V',  'Z', 0x90, 0x2d, 0x58, 0x4a, \
		0x92, 0x0b, 0x77, 0x3f, 0x1f, 0x2c, 0x55, 0x6b}
	#define UVC_GUID_FORMAT_INZI \
		{ 'I',  'N',  'Z',  'I', 0x66, 0x1a, 0x42, 0xa2, \
		0x90, 0x65, 0xd0, 0x18, 0x14, 0xa8, 0xef, 0x8a}
	#define UVC_GUID_FORMAT_INVR \
		{ 'I',  'N',  'V',  'R', 0x90, 0x2d, 0x58, 0x4a, \
		0x92, 0x0b, 0x77, 0x3f, 0x1f, 0x2c, 0x55, 0x6b}
	#define UVC_GUID_FORMAT_INRI \
		{ 'I',  'N',  'R',  'I', 0x90, 0x2d, 0x58, 0x4a, \
		0x92, 0x0b, 0x77, 0x3f, 0x1f, 0x2c, 0x55, 0x6b}
	#define UVC_GUID_FORMAT_INVI \
		{ 'I',  'N',  'V',  'I', 0xdb, 0x57, 0x49, 0x5e, \
		0x8e, 0x3f, 0xf4, 0x79, 0x53, 0x2b, 0x94, 0x6f}
	#define UVC_GUID_FORMAT_RELI \
		{ 'R',  'E',  'L',  'I', 0x14, 0x13, 0x43, 0xf9, \
		0xa7, 0x5a, 0xee, 0x6b, 0xbf, 0x01, 0x2e, 0x23}
	#define UVC_GUID_FORMAT_L8 \
		{ '2', 0x00,  0x00,  0x00, 0x00, 0x00, 0x10, 0x00, \
		0x80, 0x00, 0x00, 0xaa, 0x00, 0x38, 0x9b, 0x71}
	#define UVC_GUID_FORMAT_L16 \
		{ 'Q', 0x00,  0x00,  0x00, 0x00, 0x00, 0x10, 0x00, \
		0x80, 0x00, 0x00, 0xaa, 0x00, 0x38, 0x9b, 0x71}
	#define UVC_GUID_FORMAT_D16 \
		{ 'P', 0x00,  0x00,  0x00, 0x00, 0x00, 0x10, 0x00, \
		0x80, 0x00, 0x00, 0xaa, 0x00, 0x38, 0x9b, 0x71}

usr/src/kernel/kernel-4.4/drivers/media/usb/uvc/uvc_driver.c inside uvc_format_desc uvc_fmts[] struct:

	{
		.name		= "Raw data 8-bit (RAW8)",
		.guid		= UVC_GUID_FORMAT_RAW8,
		.fcc		= V4L2_PIX_FMT_RAW8,
	},
	{
		.name		= "Raw data 10-bit (RW16)",
		.guid		= UVC_GUID_FORMAT_RW16,
		.fcc		= V4L2_PIX_FMT_RW16,
	},
	{
		.name		= "Depth 16-bit (INVZ)",
		.guid		= UVC_GUID_FORMAT_INVZ,
		.fcc		= V4L2_PIX_FMT_INVZ,
	},
	{
		.name		= "Depth:IR 16:8 24-bit (INZI)",
		.guid		= UVC_GUID_FORMAT_INZI,
		.fcc		= V4L2_PIX_FMT_INZI,
	},
	{
		.name		= "Depth 16-bit (INVR)",
		.guid		= UVC_GUID_FORMAT_INVR,
		.fcc		= V4L2_PIX_FMT_INVR,
	},
	{
		.name		= "Depth:IR 16:8 24-bit (INRI)",
		.guid		= UVC_GUID_FORMAT_INRI,
		.fcc		= V4L2_PIX_FMT_INRI,
	},
	{
		.name		= "Infrared 8-bit (INVI)",
		.guid		= UVC_GUID_FORMAT_INVI,
		.fcc		= V4L2_PIX_FMT_INVI,
	},
	{
		.name		= "FlickerIR 8-bit (RELI)",
		.guid		= UVC_GUID_FORMAT_RELI,
		.fcc		= V4L2_PIX_FMT_RELI,
	},
	{
		.name		= "Luminosity data 8-bit (L8)",
		.guid		= UVC_GUID_FORMAT_L8,
		.fcc		= V4L2_PIX_FMT_Y8,
	},
	{
		.name		= "Luminosity data 16-bit (L16)",
		.guid		= UVC_GUID_FORMAT_L16,
		.fcc		= V4L2_PIX_FMT_Y16,
	},
	{
		.name		= "Depth data 16-bit (D16)",
		.guid		= UVC_GUID_FORMAT_D16,
		.fcc		= V4L2_PIX_FMT_Z16,
	},

/usr/src/kernel/kernel-4.4/include/uapi/linux/videodev2.h inside Vendor-specific formats macros:

	#define V4L2_PIX_FMT_RAW8     v4l2_fourcc('R', 'A', 'W', '8') /* Raw data 8-bit */
	#define V4L2_PIX_FMT_RW16     v4l2_fourcc('R', 'W', '1', '6') /* Raw data 16-bit */
	#define V4L2_PIX_FMT_INVZ     v4l2_fourcc('I', 'N', 'V', 'Z') /* 16 Depth */
	#define V4L2_PIX_FMT_INZI     v4l2_fourcc('I', 'N', 'Z', 'I') /* 24 Depth/IR 16:8 */
	#define V4L2_PIX_FMT_INVR     v4l2_fourcc('I', 'N', 'V', 'R') /* 16 Depth */
	#define V4L2_PIX_FMT_INRI     v4l2_fourcc('I', 'N', 'R', 'I') /* 24 Depth/IR 16:8 */
	#define V4L2_PIX_FMT_INVI     v4l2_fourcc('I', 'N', 'V', 'I') /* 8 IR */
	#define V4L2_PIX_FMT_RELI     v4l2_fourcc('R', 'E', 'L', 'I') /* 8 IR alternating on off illumination */


<strong>makeKernel.sh</strong>

This script applies a few patches to makefiles in the kernel source, and then compiles the kernel and modules using make.

<strong>copyImage.sh</strong>

Copies the Image file created by compiling the kernel to the /boot directory

<strong>Notes:</strong> 

The kernel source files are downloaded in a .tgz2 format. After compilation you may want to remove those files. The files are located in /usr/src You will need to use sudo to remove the files, as they are in a system area. The work directory 'sources' contains kernel_src-txt2.tbz2, you can remove that directory. The file 'source_release.tbz2' is a much larger file that holds the kernel sources as well as many other TX2 specific source packages. You can make a backup of source_release.tbz2 before deleting it.

You may want to save the newly built Image and modules to external media so that can be used to flash a Jetson image, or clone the disk image.

The copyImage.sh script copies the Image to the current device. If you are building the kernel on an external device, for example a SSD, you will want to copy the Image file over to the eMMC in the eMMC /boot directory if you are booting from the eMMC and using external storage as your root directory. 





# abpreptools-builder

## Overview
abpreptools-builder generates a recovery-flashable ZIP that writes raw partition images directly to A/B slots.  
It is designed to normalize device state before flashing a ROM, eliminating inconsistencies caused by mismatched or partially updated partitions.

## Purpose
Modern A/B devices accumulate divergence across partitions:
- firmware updated independently of system
- mixed vendor / boot / vbmeta states
- leftover data from previous ROMs

This tool produces a package that force-aligns critical partitions by flashing known-good images to both slots.

## What It Does
- Accepts an archive containing .img partitions
- Extracts all images regardless of directory structure
- Builds a recovery-flashable ZIP
- Writes partitions directly to /dev/block/by-name/*
- Flashes both _a and _b slots
- Ensures both slots are identical

## Why It Exists
AOSP-based ROMs often assume:
- consistent firmware
- matching vendor / boot chain
- valid AVB state

When this is not true:
- bootloops
- broken radio / sensors
- AVB verification failures

This tool removes that variability by enforcing a clean baseline.

## Recommended Usage
Use this before flashing AOSP or custom ROMs when:
- switching ROM bases
- downgrading or upgrading firmware
- encountering unexplained boot issues
- moving between stock and custom builds

Typical sequence:
1. Flash abpreptools
2. Flash ROM
3. Format Data

## Input Requirements
Archive must contain valid partition images, such as:
- boot.img
- vendor_boot.img
- dtbo.img
- vbmeta*.img
- firmware partitions (md1img, scp, etc.)

Images must match the target device layout.

## Output
- Recovery-flashable ZIP
- Contains:
  - META-INF/com/google/android/
  - updater-script
  - update-binary
  - partition images

## Behavior
- No mounting or filesystem operations
- Direct block writes via recovery
- No dynamic partition handling
- No payload / OTA generation

## Limitations
- Device-specific partition names must match
- Incorrect images can hard brick
- No validation of image compatibility
- Not suitable for dynamic partition flashing via super

## Safety Model
- Requires user-supplied images
- No automatic partition detection
- Assumes user understands device layout
- Writes are irreversible once executed

## Use Case
- Preparing device before AOSP flash
- Aligning A/B slots after firmware mismatch
- Converting extracted OTA images into flashable form

## Summary
abpreptools-builder enforces partition consistency across A/B slots by flashing raw images directly, providing a clean and predictable base for subsequent ROM installation.

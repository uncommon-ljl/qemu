# Original content & patches

The `*-svd.json` files were generated from the original CMSIS files:

The `*-patch.json` are patches to add content required by QEMU.

```
cd /Users/ilg/Work/qemu/gnuarmeclipse-qemu.git/gnuarmeclipse/devices/support
```

The development version of `xcdl` is:

```
/Users/ilg/My\ Files/MacBookPro\ Projects/XCDL/xcdl-js.git/bin/xcdl \
```

## CPUID

The `cpu.revision` value (a string like r0p0) is taken from SCB.CPUID, address 0xE000ED00.

For the STM devices, this value is usually gives in the _Programming manual_, the _Core peripherals_ chapter.

## Custom definitions

### CPU core capabilities

These definitions are from the [CMSIS SVD](http://www.keil.com/pack/doc/CMSIS/SVD/html/elem_cpu.html) `<cpu>` element, with some extensions.

- `name` (CM0, CM0PLUS, CM0+, CM1, CM3, CM4, CM7)
- `revision` (r0p0, values 0-99)
- `endian` (little/big/selectable)
- `mpuPresent` (true/**false**)
- `fpuPresent` (true/**false**)
- `fpuDP` (true/false, but only when FPU present) 
- `nvicPrioBits` - the number of significant bits on he left of the NVIC byte; 4 for most STM, 2 for F0
- `deviceNumInterrupts` - does not include the 16 system exceptions
- `vendorSystickConfig` (true/**false**)

QEMU extensions

- `qemuItmPresent` (true/**false**)
- `qemuEtmPresent` (true/**false**)

### qemuAlignment

The alignment extension can be defined for the device, peripherals, clusters or registers nodes. If the peripheral does not define it but is derived, the value of the original peripheral is checked.

Possible values are:

- `any`
- `word-halfWord`
- `word`

### qemuGroupName

This property is added to peripherals that have multiple instances, like GPIOA, GPIOB, USART1, USART2, etc.

It is used to correctly generate the support code.

## Devices

The commands used to generate the specifc xsvd files are:

### STM32F0x1

```
xcdl \
svd-convert \
--file "/Users/ilg/Library/xPacks/Keil/STM32F0xx_DFP/1.5.0/SVD/STM32F0x1.svd" \
--output "STM32F0x1-xsvd.json"

xcdl \
svd-patch \
--file "STM32F0x1-xsvd.json" \
--patch "STM32F0x1-patch.json" \
--output "../STM32F0x1-qemu.json" \
--code "STM32F0x1-code.c" \
--vendor-prefix "STM32" \
--device-family "F0" \
--remove "NVIC" 

```

### STM32F103xx

```
xcdl \
svd-convert \
--file "/Users/ilg/Library/xPacks/Keil/STM32F1xx_DFP/2.1.0/SVD/STM32F103xx.svd" \
--output "STM32F103xx-xsvd.json"

xcdl \
svd-patch \
--file "STM32F103xx-xsvd.json" \
--patch "STM32F103xx-patch.json" \
--output "../STM32F103xx-qemu.json" \
--code "STM32F103xx-code.c" \
--vendor-prefix "STM32" \
--device-family "F1" \
--remove "NVIC" 

```

### STM32F40x

```
xcdl \
svd-convert \
--file "/Users/ilg/Library/xPacks/Keil/STM32F4xx_DFP/2.9.0/CMSIS/SVD/STM32F40x.svd" \
--output "STM32F40x-xsvd.json"

xcdl \
svd-patch \
--file "STM32F40x-xsvd.json" \
--patch "STM32F40x-patch.json" \
--output "../STM32F40xx-qemu.json" \
--code "STM32F40x-code.c" \
--vendor-prefix "STM32" \
--device-family "F4" \
--remove "NVIC" \
--group-bitfield "RCC/PLLCFGR/PLLQ" \
--group-bitfield "RCC/PLLCFGR/PLLP" \
--group-bitfield "RCC/PLLCFGR/PLLN" \
--group-bitfield "RCC/PLLCFGR/PLLM" \
--group-bitfield "RCC/CFGR/SWS" \
--group-bitfield "RCC/CFGR/SW" \
--group-bitfield "RCC/BDCR/RTCSEL" 

```

# Missing from CMSIS SVD

- alignment for registers & peripherals: choice of word/word-halfword/any

- bitband regions (array of {name, address})

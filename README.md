# pic18usart

Assembly code to drive the EUSART module of the PIC18F family of
microcontrollers.

It reserves two buffers of 16 bytes, one for sending and one for
receiving and uses interrupts to receive and send the bytes.

It also implements the RTS/CTS flow control by using two additional
GPIOs.

The main program uses the EUSART to just echo the character received.
When it encounters a Carriage Return ('\r') it will prepend a New Line
('\n') to it.

## Motivation

This is the code I use to manage the EUSART of the PIC18F family of
microcontrollers.

There are many examples on the net on the argument but I just needed a
simple driver, tailored to my needs, that supported RX/TX interrupts
and RTS/CTS flow control.

The code provides the following functions:

  * usart_init - initialize the EUSART module
  * usart_isr - EUSART interrupt service routine to call from the main ISR
  * usart_send_nowait - send the byte in W without blocking (set the carry flag in case of failure)
  * usart_recv_nowait - receive a byte in W without blocking (set the carry flag in case of failure)
  * usart_send - send the byte in W, blocking if needed
  * usart_recv - receive a byte in W, blocking if needed
  * usart_send_h4 - send the hex nibble (0..F) pointed by FSR0
  * usart_send_h8 - send the hex byte (00..FF) pointed by FSR0
  * usart_send_h16 - send the 16bit value pointed by FSR0 (little-endian) as hexadecimal
  * usart_send_h32 - send the 32bit value pointed by FSR0 (little-endian) as hexadecimal
  * usart_send_s16 - send the 16bit value pointed by FSR0 as signed decimal
  * usart_send_u16 - send the 16bit value pointed by FSR0 as unsigned decimal
  * usart_send_str - send the null-terminated string pointed by TBLPTR
  * usart_send_nl - send a newline ("\r\n")

## Developing

To correctly build the project you need the following programs:

  * gpasm - The GNU PIC Assembler
  * gplink - The GNU PIC Linker
  * pk2cmd - PICkit2 command utility (only for flashing)

### Building

The code targets a P18F25K50 microcontroller but it could be changed
easily to other microcontrollers of the PIC18F family.

To build the project just change to the project directory and type:

```shell
$ make
```

This will build the `usart.hex` file that can be flashed to the
microcontroller.

As a side effect we get also a bunch of .lst and .map files produced
by gpasm and gplink.

### Deploying

To flash the code to the microcontroller you need the PICkit2 command
utility and a PICkit2 programmer.

With both those requirements satisfied you can just type:

```shell
$ make flash
```

Additionally you can erase the microcontroller FLASH memory with the
following command:

```shell
$ make erase
```

You can also use another kind of programmer but it's obviously not
supported by the enclosed Makefile.

## Remarks

Please note that the CTS and RTS signals are negated (they are
actually /CTS and /RTS), that is they are asserted high instead of
low.

The /CTS signal is mapped to RB1 and /RTS is mapped to RB2.

If you don't need a flow control you can remove the code checking /CTS
and setting /RTS or connect RB1 to Vcc through a 10k pull-up resistor.

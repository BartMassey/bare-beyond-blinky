# Bare Beyond Blinky

Further Adventures In Embedded Rust

Bart Massey <bart.massey@gmail.com>
PDX Rust Meetup
2024-09-05

## Last Month

* Got a "bare metal blinky" going on our MB2

* (Quick review of embedded basics)

## This Month: Play With A Peripheral

* Elecfreaks Wukong Breakout Board
  <https://www.elecfreaks.com/learn-en/microbitExtensionModule/wukong.html>

* "Breakout / Expansion" board gives an easy way to connect
  to MB2 peripherals

* This board also provides:

  * LiPo battery
  * 5v power
  * Lego brick base
  * 4-bar power indicator LEDs

  * Blue "breath" LEDs under board
  * 4 Neopixel (WS2812) RGB LEDs
  * 8 servo controllers / ports
  * 2 motor controllers / ports
  * Speaker


## "Provided" Software

* Microsoft MakeCode (PXT) "Blocks" tools

* MicroPython tools built on CODAL/C

* Much of our Rust will be reversed from these


## The "Easy" Peripherals: I2C for Breath, Servo, Motor

* I2C is a two-wire (data + clock) serial bus

* NRF52 has I2C peripheral that handles sending/receiving
  serial protocol

* Wukong has its *own* µC on board (CMS32F030, less than
  $1.00), can send simple I2C protocol to it to:
  
  * Set "breath" LED to off, breathe, or level
  * Set servo angle
  * Set motor speed and direction

  Why? Because PXT + MicroPython

* Non-blocking, because handled remotely

* No feedback, no auto-off

## The Speaker

* Magnetic speaker on bottom of Wukong attached to digital pin on MB2

* Can toggle it to make square waves; adequate for tunes

  * Can do it "just like blinky" only faster
  * Can do it with "Pulse Width Modulation" peripheral in NRF52


## SoC Peripherals

* µC/SoC contains many peripherals; NRF52833 about 34 of them

* All are programmed like I/O pins: read and write special
  "register" memory locations inside chip

* Peripheral of the Day: PWM


## PWM Peripheral

* Wiggles digital output pin in a programmed fashion
  *without CPU* once set up

* Main purpose is to make waveform "narrow" or "wider"
  to control average output power

* Really a useful tool for general waveform generation, tho
## Speaker via PWM Peripheral

* Set up PWM for a particular frequency, then just forget until
  note is played

* Saves CPU, frequency-accurate, allows concurrency

## WS2812b operation

* "Neopixel" smart LEDs are chainable (diagram)

* 24-bit data RGB (GRB) data protocol (diagram)

* Will ignore RGB and pass down chain once initialized

* Can reset pass-through with 250µs of wait time (50µs for
  older chip) — does not clear RGB

* Timing is tight here

  * Spin loop is only a few cycles at 64MHz, so hard to get right
  * Output pin HW latency is 5-20 ns
  * Screw up the timing, break the chain

# WS2812 via PWM DMA

* Can load up RAM with sequence of comp values

* Then can tell PWM to just use those values in order to
  write out a *sequence* of pulses, without CPU involvement

* PWM tick clock is 16MHz / 62.5ns, so can get accurate… enough

* Debugging this was just awful, honestly

## Conclusions and Future Work

* Not *too* much magic here: you can do this

* Mostly just big cost of getting used to things

* Future talk with `nb`, RTIC, Embassy, RTOS


## Resources

* <https://github.com/BartMassey/mb2-wukong-expansion> Main code

* <https://github.com/BartMassey/ws2812-nrf52833-pwm> Neopixel code

* <https://docs.rust-embedded.org/> Base place for Rust
  Embedded WG resources

* <https://github.com/rust-embedded/discovery-mb2> The
  "new" not-yet-debugged MB2 book

* <https://tech.microbit.org/> Lots of tech information
  for MB2

* <https://github.com/pdx-cs-rust-embedded> Many of my examples
  in various states of repair and usefulness

## Acknowledgements

* Thanks to the Rust Embedded WG and the folks of the Rust
  and Rust Embedded community

* Thanks to the patience and help of my students, friends
  and colleagues

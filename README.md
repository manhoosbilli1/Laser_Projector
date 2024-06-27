# NOTES STM Timer module 

Timer module in be in several different types 

1- timer mode 
TCNT register gets incremented by 1 after every clock cycle. the oscillating source goes through prescaler and then increments after every clock cycle. this mode can give an overflow interrupt when register is full. usually at 80Mhz and 1:1024 prescaler it's 0.89seconds. but if we want the interrupt to occure every 1 second.. we can use the preloader to not start the count from 0 but from some other value.

2- counter mode 
Instead of clock source can take any input and at rising or falling edge of signal the TCNT register increments by one. note that in this mode it also goes through the prescaler so we can set that to 1:1 to get real value


3- general timer module
all timer module consists of counter register, Auto-reload register with a preload register access and prescaler register.


# PWM Mode of timer module
In PWM mode, the timer module is clocked from an internal clock source and produces a digital waveform on the output channel pin called the PWM signal. By using output compare registers (OCR), the incrementing timer’s register value is constantly compared against this OCR register. When a match occurs the output pin state is flipped until the end of the period and the whole process is repeated.

The timer in PWM mode will produce a PWM signal at the specified frequency the user chose. The duty cycle is also programmatically controlled by its register. The PWM resolution is affected by the desired FPWM and other factors as we’ll see in the dedicated tutorials for PWM generation.

I'll be following "Discovering the STM32 Microcontroller" by 
Geoffrey Brown to get a better understanding of this controller.. too many concepts are flying right over my head as of right now. 

# Update Thu/27/June/2024 PWM working now
The pins are just outlets, the main work is done by the clock. I had to select a pin and see which timer it was attached to. these ones are all attached to timer 1 namely channel 1, 2, and 3. i enabled all of them one by one, chose the clock source as internal as the board i'm using does not have an external source. then selected following settings, note the prescaler and period value. that defined the frquency i'll get. the equation is something like (note you have to figure out which timer corresponds to which APB and use that speed instead of system clock incase its different.)

frquency = APB1 clock speed(not system) / ((Prescal + 1) * (period + 1))
in my case it was = 72 Mhz / (72*100) = 10 Khz
if want 1Khz signal then, only need to change Period. so equation becomes 

period +1 = APB1 clock / (frquency * (prescale +1))
so it should give me, 
period + 1 = 72 Mhz / (1Khz * (71+1)) = 999 + 1 = 1000

The pins were selected as: 
![Pins](Assets/pins-forpwm.png)

The settings of pwm channel was done as: 
![Pins](Assets/timer1-settings.png)


# Serial debugging working Thu/27/June/2024 
I was thinking it might not be possible to get serial messages given that debugging was available. but to my surprise because of the presence of stlink programmer on board of F3-discovery board it gave me an access to virtual com port that was using the same com port as used by ide to program it. after enabling the UART1 in asynchronous mode i opened serial console within the ide and started the connection. 

![Serial-success](Assets/serial-success.png)


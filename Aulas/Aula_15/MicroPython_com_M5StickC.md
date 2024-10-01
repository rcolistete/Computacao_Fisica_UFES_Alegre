# MicroPython com M5StickC

#### Autor : Roberto Colistete Jr. (roberto.colistete arroba gmail.com)
##### Última atualização : 01/10/2024.

<img src="https://static-cdn.m5stack.com/resource/docs/products/core/m5stickc/m5stickc_01.webp" alt="M5StickC" width="500"/>

M5StickC, US$9.99 (com cabo USB-C pequeno e caixinha), modelos de 07/2019 a 01/2020, descontinuado há poucos anos :  
[M5StickC Docs](https://docs.m5stack.com/en/core/m5stickc)  
[M5StickC @ M5Stack Store](https://m5stack.com/collections/m5-core/products/stick-c)  
ESP32 520KB SRAM, 4MB flash, TFT Colour LCD 0.96" 80x160 ST7735S, MPU6886 acell/gyro, microphone SPM1423, RTC BM8563, 2 user buttons, 1 power/reset button, 80 mAh battery, red LED, IR transmitter, USB-C (power/serial), Grove connector, 8 female headers, orange case, 48.2 mm x 25.5 mm x 13.7 mm, 15.1 g.

Ligar pressionando o botão lateral esquerdo.  
Pressionar por 6s o botão lateral esquerdo para desligar.  

Nos códigos-fonte de testes :
-  digite tecla <Tab> após '.' em final de linha para ver a lista de atributos do objeto;
- saídas/resultados  estão indentados com 1 tab a mais.



## Opções de firmware MicroPython para M5StickC :

1. [MicroPython oficial v1.23 (05/2023) ou mais recente](#1-micropython-oficial-v123-ou-mais-recente), com código-fonte aberto, mais RAM livre e menor consumo de energia. Mas tem que instalar manualmente drivers de terceiros para a tela ST7735S, acelerômetro & giroscópio MPU6886, RTC BM8563 e CI gerenciador de energia AXP192, bem como para para os vários dispositivos em 'Units' e 'C Hats'.



## 1. MicroPython oficial v1.23 ou mais recente

Vide [documentação oficial de MicroPython para ESP32](https://docs.micropython.org/en/latest/esp32/tutorial/intro.html).

É firmware [MicroPython oficial mais recente, v1.23 de 05/2024](https://micropython.org/download/M5STACK_ATOM/), com código-fonte aberto e mais recente (permite frozen modules, adicionar módulos em C, etc). Tem mais RAM livre que UIFlow/MicroPython, menor consumo de energia (ainda mais com underclocking). Mas :

* não reconhece a tela e a maioria dos sensores, pois tem poucos drivers (apa106, dht, ds18x20 e neopixel), então tem que instalar manualmente drivers de terceiros para a tela ST7735S, acelerômetro & giroscópio MPU6886, RTC BM8563 e CI gerenciador de energia AXP192, bem como para os vários dispositivos em 'Units' e 'C Hats'.


### 1.1 Instalando firmware :

Apagando flash, ligar pressionando o 3o botão (C) para entrar em modo 'flash' :  
`$ pip install esptool`  
`$ esptool.py -p /dev/ttyUSB0 -b 115200 --chip esp32 erase_flash`  
Instalando firmware [MicroPython v1.23 ou mais recente), oficial](https://micropython.org/download/M5STACK_ATOM/), para para M5Stack Atom (próximo do M5Stick C, que não tem PSRAM :  
`$ esptool.py --chip esp32 --port /dev/ttyUSB0 write_flash --flash_mode dio -z 0x1000 M5STACK_ATOM-20240602-v1.23.0.bin`  


### 1.2 Testes e exemplos :

- tem só 1 arquivo em '/pyboard', 'boot.py' (só com comentários e comandos que podem ser habilitados para webrepl, etc), que pode ser copiado usando ['rshell'](https://github.com/dhylands/rshell) (mais fácil) ou ['ampy'](https://github.com/scientifichackers/ampy) :
```
$ rshell -p /dev/ttyUSB0
> ls -l /pyboard
       139 Jan  1 2000  boot.py
> cat /pyboard/boot.py
    # This file is executed on every boot (including wake-boot from deepsleep)
    #import esp
    #esp.osdebug(None)
    #import webrepl
    #webrepl.start()
```

- dando reset apertando uma vez o botão laterial esquerdo :
```
I (439) cpu_start: Pro cpu up.
I (439) cpu_start: Application information:
I (440) cpu_start: Compile time:     Dec 20 2019 07:50:41
I (443) cpu_start: ELF file SHA256:  0000000000000000...
I (449) cpu_start: ESP-IDF:          v3.3
I (453) cpu_start: Starting app cpu, entry point is 0x40083600
I (0) cpu_start: App cpu up.
I (464) heap_init: Initializing. RAM available for dynamic allocation:
I (471) heap_init: At 3FFAE6E0 len 00001920 (6 KiB): DRAM
I (477) heap_init: At 3FFBA488 len 00025B78 (150 KiB): DRAM
I (483) heap_init: At 3FFE0440 len 00003AE0 (14 KiB): D/IRAM
I (489) heap_init: At 3FFE4350 len 0001BCB0 (111 KiB): D/IRAM
I (496) heap_init: At 40092D6C len 0000D294 (52 KiB): IRAM
I (502) cpu_start: Pro cpu start user code
I (185) cpu_start: Chip Revision: 1
W (185) cpu_start: Chip revision is higher than the one configured in menuconfig. Suggest to upgrade it.
I (189) cpu_start: Starting scheduler on PRO CPU.
I (0) cpu_start: Starting scheduler on APP CPU.
MicroPython v1.12 on 2019-12-20; ESP32 module with ESP32
Type "help()" for more information.
```

- com 'boot.py' de 'MicroPython v1.12 on 2019-12-20' no M5StickC :
```
$ rshell -p /dev/ttyUSB0
> repl
MicroPython v1.12 on 2019-12-20; ESP32 module with ESP32
import gc
gc.collect()
gc.mem_free()
    114368   # 111.7 kB
```

- ver versão de MicroPython (após boot/reset também mostra) :
```
import os
os.uname()
    (sysname='esp32', nodename='esp32', release='1.12.0', version='v1.12 on 2019-12-20', machine='ESP32 module with ESP32')
```

- [módulo 'machine'](https://docs.micropython.org/en/latest/library/machine.html) e frequência do ESP32 (default é 160 MHz, 
pode ser alterada para 20MHz, 40MHz, 80Mhz, 160MHz or 240MHz) :
```
import machine
machine.freq()
    160000000
machine.freq(240000000)
machine.freq(160000000)
machine.freq(80000000)
machine.freq(40000000)
machine.freq(20000000)
```

- help :
```
help()
    Welcome to MicroPython on the ESP32!
    
    For generic online docs please visit http://docs.micropython.org/
    
    For access to the hardware use the 'machine' module:
    
    import machine
    pin12 = machine.Pin(12, machine.Pin.OUT)
    pin12.value(1)
    pin13 = machine.Pin(13, machine.Pin.IN, machine.Pin.PULL_UP)
    print(pin13.value())
    i2c = machine.I2C(scl=machine.Pin(21), sda=machine.Pin(22))
    i2c.scan()
    i2c.writeto(addr, b'1234')
    i2c.readfrom(addr, 4)
    
    Basic WiFi configuration:
    
    import network
    sta_if = network.WLAN(network.STA_IF); sta_if.active(True)
    sta_if.scan()                             # Scan for available access points
    sta_if.connect("<AP_name>", "<password>") # Connect to an AP
    sta_if.isconnected()                      # Check for successful connection
    
    Control commands:
      CTRL-A        -- on a blank line, enter raw REPL mode
      CTRL-B        -- on a blank line, enter normal REPL mode
      CTRL-C        -- interrupt a running program
      CTRL-D        -- on a blank line, do a soft reset of the board
      CTRL-E        -- on a blank line, enter paste mode
    
    For further help on a specific object, type help(obj)
    For a list of available modules, type help('modules')
```

- lista de módulos mostra muitos módulos extras (drivers, etc, do UIFlow MicroPython firmware) :

```
help('modules')
    __main__          framebuf          ucryptolib        urandom
    _boot             gc                uctypes           ure
    _onewire          inisetup          uerrno            urequests
    _thread           machine           uhashlib          uselect
    _webrepl          math              uhashlib          usocket
    apa106            micropython       uheapq            ussl
    btree             neopixel          uio               ustruct
    builtins          network           ujson             utime
    cmath             ntptime           umqtt/robust      utimeq
    dht               onewire           umqtt/simple      uwebsocket
    ds18x20           sys               uos               uzlib
    esp               uarray            upip              webrepl
    esp32             ubinascii         upip_utarfile     webrepl_setup
    flashbdev         ucollections      upysh             websocket_helper
    Plus any modules on the filesystem
```

- [módulo 'esp'](https://docs.micropython.org/en/latest/library/esp.html) e tamanho da memória flash interna (4 MB) :
```
import esp
esp.
    __class__       __name__        LOG_DEBUG       LOG_ERROR
    LOG_INFO        LOG_NONE        LOG_VERBOSE     LOG_WARNING
    dht_readinto    flash_erase     flash_read      flash_size
    flash_user_start                flash_write     gpio_matrix_in
    gpio_matrix_out                 neopixel_write  osdebug
esp.flash_size()
    4194304   # 4MB
```

- [módulo 'esp32'](https://docs.micropython.org/en/latest/library/esp32.html), temperatura e leitura do sensor Hall do ESP32 :
```
import esp32
esp32.
    __class__       __name__        ULP             WAKEUP_ALL_LOW
    WAKEUP_ANY_HIGH                 hall_sensor     raw_temperature
    wake_on_ext0    wake_on_ext1    wake_on_touch
esp32.raw_temperature()   # in F
    123
(esp32.raw_temperature() - 32)/1.8   # in C
    50.55556
esp32.hall_sensor()
    4
```

- botões A e B estão conectados aos pinos G37 e G39, [vide documentação do M5StickC](https://docs.m5stack.com/#/en/core/m5stickc) :
```
from machine import Pin
btnA = Pin(37, Pin.IN)
btnB = Pin(39, Pin.IN)
btnA.
    __class__       value           IN              IRQ_FALLING
    IRQ_RISING      OPEN_DRAIN      OUT             PULL_DOWN
    PULL_HOLD       PULL_UP         WAKE_HIGH       WAKE_LOW
    init            irq             off             on
btnA.value()    # sem pressionar o botão frontal (abaixo da tela), botão A
    1
btnA.value()    # pressionando o botão frontal (abaixo da tela), botão A 
    0
btnB.value()    # sem pressionar o botão laterial superior, botão B
    1
btnB.value()    # pressionando o botão laterial superior, botão B
    0
```

- LED vermelho está conectado ao pino G10 como open drain, [vide documentação do M5StickC](https://docs.m5stack.com/#/en/core/m5stickc) : :
```
from machine import Pin
led = Pin(10, Pin.OPEN_DRAIN)
led.
    __class__       value           IN              IRQ_FALLING
    IRQ_RISING      OPEN_DRAIN      OUT             PULL_DOWN
    PULL_HOLD       PULL_UP         WAKE_HIGH       WAKE_LOW
    init            irq             off             on
led.value()    # LED apagado
    1
led.value(0)    # LED aceso
```

- transmissor IR está conectado ao pino G9 como open drain, [vide documentação do M5StickC](https://docs.m5stack.com/#/en/core/m5stickc) : :
```
from machine import Pin
ir = Pin(9, Pin.OPEN_DRAIN)
ir.
    __class__       value           IN              IRQ_FALLING
    IRQ_RISING      OPEN_DRAIN      OUT             PULL_DOWN
    PULL_HOLD       PULL_UP         WAKE_HIGH       WAKE_LOW
    init            irq             off             on
ir.value()    # IR desligado
    1
ir.value(0)   # IR ligado
```

- gerenciador de energia (PMIC) AXP192. Copiar para '/pyboard/lib/' o driver 'axp192.py', baseado no driver do 
[repositório 'M5StickC-uPy'](https://github.com/karfas/M5StickC-uPy) que implementa quase todas as funções do submódulo 'axp' de 
UIFlow/MicroPython, porém com adição da função 'set_LCD_brightness(brightness)', baseada na função 'ScreenBreath' do 
[driver AXP192 para Arduino](https://github.com/m5stack/M5StickC/blob/master/src/AXP192.cpp) :
```
from machine import I2C, Pin
from axp192 import AXP192
i2c = I2C(freq=400000, sda=Pin(21), scl=Pin(22))
axp = AXP192(i2c)
axp.
    __class__       __init__        __module__      __qualname__
    __dict__        setup           temperature     set_LD02
    i2c             conf            _write          _read
    _read_bits      _set_power_0x12                 button
    battery_voltage                 battery_current
    input_voltage   input_current   bus_voltage     bus_current
    battery_power   battery_charge_current          aps_voltage
    warning_level   set_sleep
axp.setup()    # inicializa/energiza LCD backlight, RTC, MPU6886, etc
axp.bus_voltage()
    5.0405     # V, USB
axp.bus_current()
    55.125     # mA
axp.aps_voltage()
   4.9854      # V, ?
axp.battery_voltage()
    4.1371     # V, bateria carregada
axp.battery_current()
    0.0        # mA
axp.temperature()
    35.60001   # C
axp.set_LCD_Brightness(10)   # 0-12    
axp.set_LD02(False)   # desliga somente LCD backlight
axp.set_sleep()   # desliga ESP32, LCD backlight, RTC, MPU6886, etc
```

- RTC nativo de MicroPython oficial para ESP32, vide [exemplo](http://docs.micropython.org/en/v1.10/esp32/quickref.html#real-time-clock-rtc), 
sendo que não segue o padarão de [RTC do MicroPython oficial](https://docs.micropython.org/en/latest/library/machine.RTC.html), não tendo 
alarme, etc.  
'RTC.datetime()' precisa ter 8 argumentos que não são bem documentados, mas vide essa 
[GitHub issue 'RTC cannot be set correctly on ESP 32 #4540'](https://github.com/micropython/micropython/issues/4540#issuecomment-580965520),
notando que dia da semana (0-6 começando com 2a-feira) é desprezado pois é calculado automaticamente.  
**O RTC nativo do ESP32 não mantém hora e data se o ESP32 for desligado (apertando por 6s o botão lateral esquerdo para desligar o M5StickC)**.
```
from machine import RTC
rtc = RTC()
rtc.
    __class__       datetime        init            memory
rtc.datetime()
    (2000, 1, 1, 5, 0, 15, 23, 883318)
rtc.datetime((2020, 3, 21, 0, 12, 48, 0, 0)) # (year, month, day, day of week, hour, minute, second, microsecond, tzinfo)
```

- I2C via ['machine.I2C'](https://docs.micropython.org/en/latest/esp32/quickref.html#i2c-bus), forma padrão do MicroPython para ESP32. 
Com barramento I2C listando AXP192 (power manager), BM8563 (RTC) e MPU6886 (accel + gyro) :
```
from machine import I2C, Pin
i2c = I2C(freq=400000, sda=Pin(21), scl=Pin(22))
i2c.
    __class__       readinto        start           stop
    write           init            readfrom        readfrom_into
    readfrom_mem    readfrom_mem_into               scan
    writeto         writeto_mem     writevto
i2c.scan()
    [52, 81, 104]   # default I2C address of AXP192 is 0x34 = 52, BM8563 is 0x51 = 81, MPU6886 is 0x68 = 104
```

- ADC nativo do ESP32 é de 12 bits, em MicroPython limitado aos pinos 32 a 39, sendo que [no M5StickC](https://docs.m5stack.com/#/en/core/m5stickc) 
o pino G36 está exposto no barramento de 8 pinos para C-HAT's e os pinos G32 e G33 estão no conector Grove.  
Vide [documentação oficial de MicroPython para ESP32 usando ADC](https://docs.micropython.org/en/latest/esp32/quickref.html#adc-analog-to-digital-conversion).  
Notar que **o ADC do ESP32 tem diversos problemas** : offset (60-130 mV), não-linearidade e alto ruído (logo a resolução efetiva é uns 
4-5 bits menor que a nominal), vide [comparação com ADS1115](https://github.com/bboser/IoT49/blob/master/doc/analog_io.md).
```
import machine
machine.ADC.
    __class__       __name__        read            __bases__
    ATTN_0DB        ATTN_11DB       ATTN_2_5DB      ATTN_6DB
    WIDTH_10BIT     WIDTH_11BIT     WIDTH_12BIT     WIDTH_9BIT
    atten           read_u16        width
adc = machine.ADC(machine.Pin(36))
adc
    ADC(Pin(36))
adc.
    __class__       read            ATTN_0DB        ATTN_11DB
    ATTN_2_5DB      ATTN_6DB        WIDTH_10BIT     WIDTH_11BIT
    WIDTH_12BIT     WIDTH_9BIT      atten           read_u16
    width
adc.atten(machine.ADC.ATTN_11DB)    # change input attenuation to 11dB (i. e., 0.0-3.9 V)
adc.read()
    4095    # 4095 is full scale, connected to 3V3 
adc.read()
    0       # 0 is zero of scale, connected to GND
adc.width(machine.ADC.WIDTH_10BIT)   # change resolution to 10 bits (0-1023)
adc.read()
    1023    # 1023 is full scale, connected to 3V3  
adc.read()
    0       # 0 is zero of scale, connected to GND
```

- DAC nativo do ESP32 é de 8 bits em 2 pinos (G25 e G26), sendo que o pino G26 está exposto no barramento de 8 pinos do M5StickC para C-HAT's.
A documentação oficial de MicroPython para ESP32 ainda não cita DAC, dentro do módulo 'machine', mas a 
[documentação para pyb.DAC](https://docs.micropython.org/en/latest/library/pyb.DAC.html) pode ser em parte aproveitada, i. e., só comando 'write()'.  
```
import machine
dac = machine.DAC(machine.Pin(26))
dac.
    __class__       write
dac.write(0)     # 0.074 V measured with voltimeter on GND & G26
dac.write(64)    # 0.843 V
dac.write(128)   # 1.616 V
dac.write(192)   # 2.387 V
dac.write(255)   # 3.144 V, it should be = 3V3 pin (3.272 V on voltimeter)
```

- (FAZER) exemplo de uso de tela ST7735S com drivers externos;

- (FAZER) uso de MPU6882 com driver externo :

- (FAZER) uso de RTC BM8563 com driver externo :

- (FAZER) uso de microphone SPM1423, com driver externo :




### 1.3 Consumo de energia

#### 1.3.1 Medição de energia do 'M5StickC' usando [UM25C](https://www.aliexpress.com/item/32855845265.html) com cabo USB-C, mas pode estar carregando a bateria :

- 28 mA sem conectar via USB serial/terminal REPL (após 'Ctrl+A+K' no 'screen' para fechar o terminal), 40 mA com terminal REPL;

- desligando via botão power/reset (6s), mantém consumo de 5.8 mA (desativando terminal REPL antes) a 18 mA ! Parece que o 
CI gerenciador de energia AXP192 fica ativo para carregar bateria, etc, ao menos quando a alimentação USB é usada;

- 15-37/27-49 mA variando frequência do ESP32 entre 20 MHz, 40 MHz, 80 MHz, 160 MHz e 240 MHz :
```
import machine
machine.freq(240000000)   # 37/49 mA
machine.freq(160000000)   # default, 28/40 mA
machine.freq(80000000)    # 24/36 mA
machine.freq(40000000)    # 17/29 mA
machine.freq(20000000)    # 15/27 mA
```

- 28/40 mA -> 51/63 mA com LED vermelho ligado :
```
from machine import Pin
led = Pin(10, Pin.OPEN_DRAIN)
led.value(0)    # LED aceso
```

- 28/40 mA -> 56/68 mA com transmissor IR ligado :
```
from machine import Pin
ir = Pin(9, Pin.OPEN_DRAIN)
ir.value(0)    # IR ligado
```

- 28/40 mA -> 50/62 mA ao ligar AXP192 (& LCD backlight, RTC, MPU6886, etc) :
```
from machine import I2C, Pin
from axp192 import AXP192
i2c = I2C(freq=400000, sda=Pin(21), scl=Pin(22))
axp = AXP192(i2c)
axp.setup()    # inicializa/energiza LCD backlight, RTC, MPU6886, etc, i aumenta para 62 mA
axp.set_LD02(False)   # desliga somente LCD backlight, i cai para 42 mA
axp.set_sleep()   # desliga ESP32, LCD backlight, RTC, MPU6886, etc
```

- 50/62 mA -> 30/42 mA com 'axp.set_LCD_brightness(0)', coloca a tela em modo de economia de energia :
```
from machine import I2C, Pin
from axp192 import AXP192
i2c = I2C(freq=400000, sda=Pin(21), scl=Pin(22))
axp = AXP192(i2c)
axp.setup()    # inicializa/energiza LCD backlight, RTC, MPU6886, etc, i aumenta para 62 mA
axp.set_LCD_brightness(0)    # 42 mA (apagada)
axp.set_LCD_brightness(8)    # 43 mA (bem escura)
axp.set_LCD_brightness(9)    # 46 mA (brilho médio, legível)
axp.set_LCD_brightness(11)   # 51 mA (brilho forte, bem legível)
axp.set_LCD_brightness(12)   # 62 mA (brilho muito forte, muito legível)
```

- 28/40 mA -> 7/19 mA com 'machine.deepsleep(30000)', desliga só o ESP32, reinicializa em 30s :
```
import machine
machine.deepsleep(30000)
```

Consumo de energia por itens :  
- ESP32 : 9/11/18/22/31 mA em idle (sem rodar nada) @ 20/40/80/160/240 MHz; 
- AXP192, etc : 6-18 mA (sem terminal/com terminal habilitado) ao desligar via botão power/reset (6s), ou 7/19 mA em 'deepsleep()';
- tela : 20 mA com luz de fundo;
- LED vermelho : 23 mA;
- transmissor IR : 28 mA.

# SensorDisplay
Для подключения сенсорного монитора к системе UBUNTU нет достаточного количества настроек для комфортной работы. Я столкнулся с тем что при подключении сенсорного дисплея как дополнительного, все тапы по сенсорному экрану откликаются только на основном экране. Стандартных настроек через GUI я не обнаружил.

## Варианты решения проблемы
1. Использовать сенсорный дисплей как основной указав это в GUI настройках системы.
2. Если сенсорный дисплей нужно подключить не как основной то тогда необходимо выяснить какой видео интерфейс задействован в операционной системе сенсорным монитором, а также установить идентификатор устройства ввода сенсорного дисплея и выполнить сопоставление этих значений в ОС.

Сопоставлением этих данных в LINUX занимается утилита командной строки **xinput**.

`xinput map-to-output $VALUE_MON_ID $VIDEO_INTERFACE`

где `$VALUE_MON_ID` это идентификатор устройства сенсорного дисплея, 
а `$VIDEO_INTERFACE` это видео интерфейс через который работает сам сенсорный дисплей

Значение этих параметров можно увидеть используя следующие командные утилиты
   - `xrandr`
   - `xinput`

Утилита **xrandr** сделает вывод всех видео интерфейсов присутствующих в ОС и отобразит из них активные и не активные. У меня есть сенсорный монитор который подключен через интерфейс **HDMI-0**

![image](https://github.com/Amursky1988/SensorDisplay/assets/82404908/6e5bdd66-e205-42ae-817b-7d7feb9c2ca8)

Утилита **xinput** выведет все зарегистрированные физические и виртуальные устройства ввода с указанием его идентификатора, в моем случае это **id=14**

![image](https://github.com/Amursky1988/SensorDisplay/assets/82404908/db04959f-03e2-4063-a8be-86194ddb88bd)


Выполнив команду 

`xinput map-to-output 14 HDMI-0`

Произведем сопоставление идентификатора устройства ввода с сенсорным дисплеем, после чего экран начнет адекватно реагировать на прикосновения. 
## Проблемы
Все детали трудностей применения такого подхода к определению этих параметров зависят насколько часто вы будите переподключать свой сенсорный дисплей, ведь при подключении дисплея к другому usb порту или видео выходу видео карты обязательно будет сопровождаться сменой идентификатора устройства ввода и сменой интерфейса видеовыхода. И вам снова нужно будет смотреть новые значения. Так же после каждого включения ПК нужно будет снова применять сопоставление значений. Если для вас это не проблема то вы можете и дальше использовать команду

`xinput map-to-output` 

Но если переподключение дисплея носит постоянный характер то это дело можно немного автоматизировать с помощью сценария bash.
Суть проста, брать полученные значения **xrandr** и **xinput** и фильтровать эти данные при помощи стандартного набора утилит которые присутствуют в Ubuntu, предварительно определив уникальные параметры для вашего монитора. 
## Что можно было бы улучшить
Данный скрипт я назначил на хоткей и пока в этом режиме мне его хватает. Так же скрипт можно добавить в автозагрузку при входе пользователя в систему, это избавит вас от постоянного запуска скрипта вручную при включении ПК.

Для этого необходимо создать файл с расширением **.desktop** например **sensor.desktop** в любой из указанных директорий

* **/etc/xdg/autostart/** - для всех пользователей;
* **~/.config/autostart/** - для текущего пользователя.

Файл скрипта поместить в каталог **/usr/local/bin**

Содержание файла должно быть следующим

```
[Desktop Entry]
Name=sensor
Type=Application
Exec=/usr/local/bin/sensor.sh
```
Где **Exec** это путь до скрипта.

В дальнейшем было бы неплохо сделать так что при любом изменении состава вывода утилит  **xrandr** и **xinput** выполнять данный скрипт, который бы снова переопределял новые значения для сенсорного монитора


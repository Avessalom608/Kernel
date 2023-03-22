# kernel compilation (RU)
Сборка ядра + драйвер nvidia
Учти, что по такой инструкции я собирал ядро 6.1.20 для manjaro KDE, 
любые отклонения возможно могут либо создать новые проблемы, либо не будет имеющихся.
# Ядро Kernel

1. скачиваем исходные файлы ядра с kernel.org, разархивируем 

2. (Опционально) устаналиваем патч для более тонкой настройки архитектуры процессора
https://github.com/graysky2/kernel_compiler_patch.git

2.1. скачиваем и распаковываем. 

2.2. Перемещаем файл more-uarches-for-kernel-5.17+.patch в директорию с ядром

2.3. В директории с ядром и патчем вводим patch -p0 <  more-uarches-for-kernel-5.17+.patch

2.4. На просьбы указать файл указываем по очереди arch/x86/Kconfig.cpu, arch/x86/Makefile arch/x86/include/asm/vermagic.h. Готово.

![image](https://user-images.githubusercontent.com/40124505/226691623-73590f95-f6a1-481f-876d-4b6aa8d0df54.png)


3. очищаем от компилированных файлов make mrproper -j4 (Где -J4 кол — во ядер)
4. либо копируем стандартную конфигурацию zcat /proc/config.gz > .config, 
   либо оставляем только включённые модули в системе (Важно подключить все устройства, которые будут использоваться)  make localmodconfig -j4 (Рекомендовано)
5. (Опционально, но желательно) открыть .config и изменить значение строки CONFIG_LOCALVERSION на своё. Например вместо "Manjaro" написать "NeManjaro"
6. Запускаем редактор ядра (Если на кде — то make xconfig)

6.1. (Опционально) заходим в General Setup и в разделе Kernel compression mode ставим LZMA сжатие (вроде как самое эффективное), отключаем Enable BPF LSM Instrumentation (иначе будет ошибка в логе,но не должно помешать загрузке), в Enable loadable module support > Module compression mode ставим значение none.

6.2. После того, как определились с настройками, можно и Собирать ядро make -j4

7. Собираем модули sudo make modules -j4
и устанавливаем их sudo make modules_install -j4 (Где -j4 это кол - во ядер)

8. Копируем собранное ядро sudo cp -v arch/x86_64/boot/bzImage /boot/vmlinuz-6.1.20-kingKernel 
(Где «kingKernel» можно ставить любое название по желанию)
9. Создаём файл инициализации sudo mkinitcpio -k 6.1.20-kingKernel -g /boot/initramfs-6.1.20-kingKernel.img
(Где «-k 6.1.20-kingKernel» нужно укзаать папку с модулями в /lib/modules/ (Например /lib/modules/6.1.20-kingKernel)

10. Обновляем загрузчик sudo grub-mkconfig -o /boot/grub/grub.cfg.

# Модуль Nvidia
Если у тебя стоят проприетарные драйвера Nvidia, то скорее всего ядро не запустится, поэтому следуй инструкции:
1. скачиваем модуль https://github.com/NVIDIA/open-gpu-kernel-modules, распаковываем его.
2. Скачиваем драйвер видеокарты в формате run с сайта www.nvidia.com

![image](https://user-images.githubusercontent.com/40124505/226967992-713b23a8-5c52-407e-b562-786f0ad41ace.png)


2. Теперь тебе нужно зайти в пока что ещё не рабоющее ядро и отрыть tty (alt + ctrl + F2)
3. Заходим в директорию с распакованным модулем и выполняем знакомые нам команды
sudo make modules -j4 и потом sudo make modules_install -j4. Модуль по сути теперь установлен.
4. запускаем драйвер NVIDIA-Linux-x86_64-525.89.02.run
5. Соглашаемся до момента, когда спросят об установке 32 битных библиотек







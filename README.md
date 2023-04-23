# Компиляция Ядра + модуль Nvidia (RU)

Учти, что по такой инструкции я собирал ядро 6.1.20 для manjaro KDE, 
любые отклонения возможно могут либо создать новые проблемы, либо не будет имеющихся.
# Ядро 

1. скачиваем исходные файлы ядра с kernel.org, разархивируем 

2. (Опционально) устаналиваем патч для более тонкой настройки архитектуры процессора [patch](https://github.com/graysky2/kernel_compiler_patch.git)

2.1. скачиваем и распаковываем. 

2.2. Перемещаем файл ``more-uarches-for-kernel-5.17+.patch`` в директорию с ядром

2.3. В директории с ядром и патчем вводим: 

      patch -p0 <  more-uarches-for-kernel-5.17+.patch

2.4. На просьбы указать файл указываем по очереди ``arch/x86/Kconfig.cpu`` ``arch/x86/Makefile`` ``arch/x86/include/asm/vermagic.h`` 
Готово.

![image](https://user-images.githubusercontent.com/40124505/226691623-73590f95-f6a1-481f-876d-4b6aa8d0df54.png)


3. очищаем от компилированных файлов:

         make mrproper -j4  
         
``(Где -J4 кол — во ядер)``

5. Теперь либо копируем стандартную конфигурацию 

        zcat /proc/config.gz > .config, 
 
 либо оставляем только включённые модули в системе (Важно подключить все устройства, которые будут использоваться) (Рекомендовано)  

      make localmodconfig -j4 

5. (Опционально, но желательно) открыть .config и изменить значение строки CONFIG_LOCALVERSION на своё. Например вместо "Manjaro" написать "NeManjaro"
6. Запускаем редактор ядра ``KDE = make xconfig: GNOME = make gconfig: либо с псевдографикой = make menuconfig``

6.1. (Опционально) заходим в General Setup и в разделе Kernel compression mode ставим LZMA сжатие (вроде как самое эффективное), отключаем Enable BPF LSM Instrumentation (иначе будет ошибка в логе,но не должно помешать загрузке), в Enable loadable module support > Module compression mode ставим значение none.

6.2. После того, как определились с настройками, можно и Собирать ядро:

      make -j4

7. Собираем и устанавливаем модули ядра:
   
         sudo make modules -j4 && sudo make modules_install -j4 
   
(Где -j4 это кол - во ядер)

8. Копируем собранное ядро 

         sudo cp -v arch/x86_64/boot/bzImage /boot/vmlinuz-6.1.20-CustomKernel 

``(Где «CustomgKernel» можно ставить любое название по желанию)``

9. Создаём файл инициализации 

         sudo mkinitcpio -k 6.1.20-CustomKernel -g /boot/initramfs-6.1.20-CustomKernel.img

`Где «-k 6.1.20-CustomKernel» нужно укзаать папку с модулями в /lib/modules/ (Например /lib/modules/6.1.20-CustomKernel`

10. Обновляем загрузчик 

         sudo grub-mkconfig -o /boot/grub/grub.cfg.

# Модуль Nvidia
Если у тебя стоят проприетарные драйвера Nvidia, то скорее всего ядро не запустится, поэтому следуй инструкции:
1. скачиваем модуль [module](https://github.com/NVIDIA/open-gpu-kernel-modules), распаковываем его.
2. Скачиваем драйвер видеокарты в формате run с сайта www.nvidia.com

![image](https://user-images.githubusercontent.com/40124505/226967992-713b23a8-5c52-407e-b562-786f0ad41ace.png)


2. Теперь тебе нужно зайти в пока что ещё не рабоющее ядро и отрыть tty (alt + ctrl + F2)
3. Заходим в директорию с распакованным модулем и выполняем компиляцию и установку

         sudo make modules -j4 && sudo make modules_install -j4. 

Модуль по сути теперь установлен.

4. запускаем драйвер NVIDIA-Linux-x86_64-525.89.02.run

# Удаление ядра и модулей

1. Стоит вначале понять что пошло не так, для примера в неработающем ядре в tty ввести:  

         journalctl --boot ---priority 4

2. Загрузиться в работающее ядро
3. Удаляем ядро, модули и initramfs
 
         sudo rm -fr /boot/vmlinuz-6.1.20-CustomKernel &&  sudo rm -fr /lib/modules/6.1.20-CustomKernel && sudo rm -r /boot/initramfs-6.1.20-CustomKernel.img 
        
4. снова обновляем загрузчик:

            sudo grub-mkconfig -o /boot/grub/grub.cfg.







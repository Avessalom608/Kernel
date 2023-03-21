# kernel compilation (RU)
Сборка ядра + драва нвидиа
Учти, что по такой инструкции я собирал ядро 6.1.20 для manjaro KDE, 
любые отклонения возможно могут либо создать новые проблемы, либо не будет имеющихся.
# Ядро Kernel

1. скачиваем исходные файлы ядра с kernel.org, разархивируем 
2. (Опционально) устаналиваем патч для более тонкой настройки архитектуры процессора
https://github.com/graysky2/kernel_compiler_patch.git
2.1. скачиваем и распаковываем. 
2.2. Перемещаем файл more-uarches-for-kernel-5.17+.patch в директорию с ядром
2.3 В директории с ядром и патчем вводим patch -p0 <  more-uarches-for-kernel-5.17+.patch
2.4 На просьбы указать файл указываем по очереди arch/x86/Kconfig.cpu, arch/x86/Makefile arch/x86/include/asm/vermagic.h
![image](https://user-images.githubusercontent.com/40124505/226690545-247cfa64-9a53-401f-b943-89301c24eb0c.png)



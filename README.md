# types
Types of virtualizations

**Виртуализация** — это создание изолированных окружений в рамках одного физического устройства. Каждое окружение выглядит, как отдельный компьютер со своими характеристиками (доступная память, процессор и тп.) 

Такое окружение называют набором **логических ресурсов** или **виртуальной машиной**.

ОС, внутри которой стартует другая ОС, называется **хост-системой, (host)**. А ОС, которая работает в виртуальном окружении — **гостевой (guest)**.

**WSL** - Разработчики могут получить доступ к возможности Windows и Linux одновременно на компьютере с Windows. Подсистема Windows для Linux (WSL) позволяет разработчикам устанавливать дистрибутив Linux (например, Ubuntu, OpenSUSE, Kali, Debian, Arch Linux и т. д.) и использовать приложения Linux, служебные программы и программы командной строки Bash непосредственно в Windows без изменений, без дополнительных затрат на традиционную виртуальную машину или двойную установку.

**Виртуализация бывает:**
- Аппаратная
- Программная
- Контейнерная (будет рассмотрена в следующих лекциях)
- Хостинговая (рассматривалась на первой лекции)

**Аппаратная виртуализация** работает благодаря поддержке со стороны железа: процессора. В отличие от программной виртуализации, гостевые ОС управляются гипервизором напрямую, без участия хостовой ОС.
- + Работает без посредников, быстро, весело, надежно.
- - Привязана к платформе: нет технологии — нет виртуализации.

**Программная виртуализация** эмулирует все железо от процессора до сетевого адаптера (если он нужен). В отличие от аппаратной виртуализации, не важно, какое у вас аппаратное обеспечение, будет работать на любом железе.
- + Хост система и гостевая система не зависимы.
- - Медленная, так как надо эмулировать все, что работает быстро.

**История создания**
- Первая аппаратная виртуализация Intel была воплощена в 386-х процессорах и носила название **V86 mode**.  Этот режим работы 8086-го процессора позволял запускать параллельно несколько DOS-приложений
- Второе, что было реализовано — **многозадачность**. Она является первым уровнем абстракции приложений. Каждое приложение распределяет ресурсы физического процессора в режиме разделения исполнения кода по времени.
- **HyperThreading** представляет собой аппаратную технологию виртуализации: при ее использовании происходит симуляция двух виртуальных процессоров в рамках одного физического с помощью техники Symmetric Multi Processing (SMP).
- Процессор с поддержкой виртуализации может работать в двух режимах **root operation** и **non-root operation**. В режиме root operation работает специальное программное обеспечение, являющееся «легковесной» прослойкой между гостевыми операционными системами и оборудованием — монитор виртуальных машин (Virtual Machine Monitor, VMM), носящий также название гипервизор **(hypervisor)**.

Технологии аппаратной виртуализации
- Intel — VT-x VT-d VMDQ
- AMD — AMD-V
- ARM Limited — EL2

Ведущие производители процессоров для серверных и настольных платформ — компании Intel и AMD разработали техники аппаратной виртуализации, для их использования в платформах виртуализации.

**AMD** - Аппаратная виртуализация — это логическое развитие архитектуры AMD Direct Connect, реализующая технологию виртуализации в кремнии. Эта технология дает больше возможностей производителям программного обеспечения, позволяя не беспокоиться о программной эмуляции виртуализации на процессоре.

Аппаратная виртуализация AMD реализуется путем введения новых режимов работы процессора и дополнительных инструкций:
- Новый режим процессора: Guest Mode
- Новая структура данных: Virtual Machine Control Block (VMCB)
- Новая инструкция: VMRUN
- Новый режим памяти: Real Mode w/ Paging

![amd](https://github.com/joos-virt/types/blob/main/amd.png)

**Intel** - Аппаратная виртуализация VT-x описывается специальной структурой VMCS (Virtual Machine Control Structure) и является небольшим участком физической оперативной памяти, хранящим:
- минимально необходимые данные для запуска гостевой ОС,
- данные, необходимые для безопасного выхода из режима работы гостевой ОС,
- некоторые настройки, относящиеся к управлению этой виртуальной машиной.

![intel](https://github.com/joos-virt/types/blob/main/intel.png)

**ARM** гипервизор работает с уровнем исключения EL2. Только программное обеспечение, работающее на уровне исключения EL2 или выше, может получить доступ и настроить различные функции виртуализации.

![arm](https://github.com/joos-virt/types/blob/main/arm.png)

**KVM** - Для исполнения прямых аппаратных запросов в ОС должна иметься библиотека, которая направляла бы эти запросы аппаратной части напрямую. Red hat создала ассоциацию Open Virtualization Alliance, которая была признана решить проблему отсутствия базового гипервизора для ядра Linux. Так и был создан гипервизор KVM или Kernel-based Virtual Machine.

**Принцип KVM** - Гипервизор KVM представляет из себя загружаемый модуль ядра Linux, который предназначен для обеспечения виртуализации на платформе Linux x86. Сам модуль содержит:
- компонент собственно виртуализации (kvm.ko),
- процессорно-специфический загружаемый модуль kvm-amd.ko либо kvm-intel.ko

- Начиная с версии ядра 2.6.20, KVM является основной составляющей Linux. Иными словами, если у вас стоит Linux, то у вас уже есть KVM.
- Необходимым условием для использования KVM является поддержка одной из инструкций виртуализации — Intel VT-x, AMD-V или ARP с поддержкой EL2
- Сам по себе KVM не выполняет эмуляции. Вместо этого программа, работающая в пространстве пользователя, использует интерфейс /dev/kvm для настройки адресного пространства гостя виртуальной машины, через него же эмулирует устройства ввода- вывода и видеоадаптер.
- KVM позволяет виртуальным машинам использовать немодифицированные образы дисков QEMU, VMware и других, содержащие ОС.
- Каждая виртуальная машина имеет свое собственное виртуальное аппаратное обеспечение:
  - сетевые карты,
  - диск,
  - видеокарту,
  - и другие устройства

**libvirt** — это набор инструментов, предоставляющий единый API к множеству различных технологий виртуализации.

При использовании libvirt не важно какой «бекенд»: Xen, KVM, VirtualBox или что-то ещё. Более того, можно использовать libvirt внутри Ruby, Python, C++ и многих других программ Или удаленно подключаться к виртуальным машинам по защищенным каналам.

**libvirt** — это просто API, а вот как с ним взаимодействовать решать пользователю.
```
egrep --color=auto 'vmx|svm|0xc0f' /proc/cpuinfo
```
проверим, поддерживается ли аппаратная виртуализация. На самом деле, работать будет и без её поддержки, только гораздо медленнее.

Так как KVM — это модуль ядра Linux, то нужно проверить,загружен ли он уже, и если нет, то загрузить:
```
lsmod | grep kvm
```
Если модуль не загружен:
```
modprobe kvm
modprobe kvm_intel # или modprobe kvm_amd
```
Возможна ситуация, что аппаратная виртуализация выключена в BIOS. Поэтому, если модули kvm_intel/kvm_amd не подгружаются, то необходимо проверить настройки BIOS.

Теперь установим необходимые пакеты. Проще всего сделать это, установив сразу группу пакетов:
```
CentOS : yum group list «Virtual*»
```
Для управления виртуальными машинами из командой строки используется утилита virsh (virsh умеет создавать виртуалки только из XML файлов,). Проверить, есть ли хотя бы одна виртуалка, можно командой:
```
virsh list
```
Для использование GUI — virt-manager.

Пример использования libvirt
```
virt-install --name mkdev-vm-0 \
--location ~/Downloads/CentOS-7-x86_64-Minima-2009l.iso \
--memory=1024 --vcpus=1 \
--disk size=8
```

**Xen** — кроссплатформенный гипервизор, разработанный в компьютерной лаборатории Кембриджского университета и распространяемый на условиях лицензии GPL.Основные особенности:
- поддержка режима паравиртуализации помимо аппаратной виртуализации,
- минимальность кода самого гипервизора, за счёт выноса максимального количества компонентов за пределы гипервизора.

**HYPER-V** система аппаратной виртуализации для x64-систем на основе гипервизора. Все они позволяют виртуализировать серверные платформы x86-64 и представляют собой системы аппаратной виртуализации для VPS хостинга и не только.

**QEMU** — свободная программа с открытым исходным кодом для эмуляции аппаратного обеспечения различных платформ. Включает в себя эмуляцию процессоров Intel x86 и устройств ввода-вывода. Может эмулировать 80386, 80486, Pentium, Pentium Pro, AMD64 и другие x86-совместимые процессоры; ARM, MIPS, RISC-V, PowerPC, SPARC, SPARC64 и частично m68k.

**Режимы QEMU**
- **Полная эмуляция системы** — полностью эмулирует устройство, включая все его компоненты, процессор и различные периферийные устройства. Он может использоваться для запуска нескольких ОС без перезагрузки или отладки системного кода.
- **Эмуляция пользовательского режима** — работает только для Linux хоста, позволяет запускать процессы Linux, скомпилированные для одной архитектуры в другой, например ARM программы в x86. Полезно для разработки, кросс- компиляции и отладки

Установка
```
sudo apt install qemu-kvm qemu qemu-system
```
Демонстрация создания виртуальной машины:
```
qemu-img create -f qcow2 test 1G
qemu-system-x86_64 -hda ubuntu.qcow -boot d -cdrom
~/downloads/name_iso.iso -m 640
```
Эмулятор qemu создает много команд, но их можно разделить на группы:
- qemu-архитектура — эмуляция окружения пользователя для указанной архитектуры;
- qemu-system-архитектура — эмуляция полной системы для архитектуры;
- qemu-img — утилита для работы с дисками;
- qemu-io — утилита для работы с вводом/выводом на диск;
- qemu-user — оболочка для qemu-архитектура, позволяет запускать программы других архитектур в этой системе;
- qemu-system — оболочка для qemu-system-архитектура, позволяет полностью эмулировать систему нужной архитектуры

Graphical Network Simulator (GNS3) — это графический симулятор сети, который позволяет смоделировать виртуальную сеть из маршрутизаторов и виртуальных машин. Незаменимый инструмент для обучения и тестов. Работает практически на всех платформах. Отлично подходит для создания стендов на десктоп машинах. В зависимости от аппаратной платформы, на которой будет использоваться GNS3, возможно построение комплексных проектов, состоящих из маршрутизаторов Cisco, Cisco ASA, Juniper, а также серверов под управлением сетевых операционных систем.


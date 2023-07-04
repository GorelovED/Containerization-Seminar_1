# Контейнеризация (семинары)


![picture for containerization](https://github.com/Terekhov-A-S/Containerization-Seminar_1/blob/main/containerization.jpg)

## Урок 1. Механизмы пространства имен

### **Информация о проекте**

Необходимо продемонстрировать изоляцию одного и того же приложения (как решено на семинаре - командного интерпретатора) в различных пространствах имен.

### **Как сдавать проект** 

Формат сдачи ДЗ: предоставить доказательства в виде скриншота и текстового документа с введенными командами.


**Задание**

* Запустим Bash в новом пространстве имен командой (так как команда требует привилегии суперпользователя, то выполняем через sudo):
```
sudo unshare -pf -n --mount-proc bash
```
![sudo unshare -pf -n --mount-proc bash](https://raw.githubusercontent.com/Terekhov-A-S/Containerization-Seminar_1/main/source/19-21-02.png)


* В параллельном терминале смотрим, что произошло (наглядно):
```
ps -afx
```
![ps -afx](https://raw.githubusercontent.com/Terekhov-A-S/Containerization-Seminar_1/main/source/19-23-13.png)

Наблюдаем несколько родительских процессов, которые породили одинаковые процессы Bash. Заметим, что PID у нового процесса 4400.
![ps -afx](https://raw.githubusercontent.com/Terekhov-A-S/Containerization-Seminar_1/main/source/19-22-19.png)

Для проверки и наглядности смотрим той же командой ps -afx в изолированном терминале
![ps -afx](https://raw.githubusercontent.com/Terekhov-A-S/Containerization-Seminar_1/main/source/19-23-48.png)
Здесь мы сожем видеть, что изолированная оболчка видит всего два процесса (и то, второй процесс сразу же, после выполнения команды, исчезнет). При этом PID процессов начинается с 1.


* Далее, из этого же терминала, пробуем запустить пинг любого сайта, например ya.ru.
```
ping ya.ru
```
 ![ps -afx](https://raw.githubusercontent.com/Terekhov-A-S/Containerization-Seminar_1/main/source/19-27-21.png)
 Наблюдаем, что пинг до указанного сайта не может быть осуществлен, так как сеть в этом пронстранстве имен имеется только локальная, т.е. localhost.


А теперь, ту же команду запустим из параллельного терминала (котоый не изолирован).
![ps -afx](https://raw.githubusercontent.com/Terekhov-A-S/Containerization-Seminar_1/main/source/19-27-46.png)
Видим, что пакеты до сайта уходят нормально и ответ от сервера принимается.


* Теперь, в оба терминала отправляем команду $ hostname, что бы увидеть наш хост.
```
hostname
```
![ps -afx](https://raw.githubusercontent.com/Terekhov-A-S/Containerization-Seminar_1/main/source/20-00-38.png)
Как мы можем заметить - хоcт и там и там одинаков. А теперь, в изолированном терминале выполняем команду:
```
sudo unshare -u bash
```
Команда $ unshare запускает программу (опционально) в новом namespace. Флаг -u говорит ей запустить bash в новом UTS namespace. Обратите внимание, что наш новый процесс bash указывает на другой файл UTS, тогда как все остальные остаются прежними. 

Одним из следствий того, что мы только что проделали, является то, что теперь мы можем изменить системный hostname из нашего нового процесса bash и это не повлияет ни на какой другой процесс в системе. 
Изменим наш хост в изолированном терминале, например:
```
hostname geekbrains
```
Эта команда никак не затронула хост основной системы. Можем проверить это, выполнив hostname в первом терминале и увидев, что имя хоста там не изменилось.
![ps -afx](https://raw.githubusercontent.com/Terekhov-A-S/Containerization-Seminar_1/main/source/20-05-01.png)


Этими манипуляциями мы доказали, что научились запускать процессы в разных namespace с возможностью опционально изолировать нужные нам направления.



*Подготовил студент Geek Brains* [**`Терехов Александр`**](https://gb.ru/users/7696463), containerization-Seminar_1

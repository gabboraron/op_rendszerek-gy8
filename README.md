# GY8
> mappa: `gy3`

### Négtelen cső:
- olyan mint egy sor
- a 0 vég az olvasó vég, 1 es vég az író vég
- ezt `write`tal és `read`del lehet kezelni, meg kell adni a memóriacíemet, a read pedig olvasni fog a 0tól, meg kell adni egy címet és oda kell bemásolni annyi adatot amennyit a csővezetékben talál
- a nem használt végeket zárjuk le!
- - az olvasó fél zárja le az író véget
- egyetlen csővezetéken is lehet oda vissza kommunikációt folytatni, de ha a szülő beléépp és írja és közvetlenül utánna olvasunk akkor akkor ha a szülő az aki végrehajtja az utasítást akkor a sjátmaga által írt adatot fogja beolvasni
- - zh-n ajánlott két csővezeéket használni: egyiken írunk, másikon olvasunk
- - ha nagyon sok folyamat közt kell oda vissza kommunikációt akkor nem éri meg duplázni a csővezetékékeket
- - írunk egy üzenetet, és várunk a másik fél jelzésére, hogy elküldte-e már a választ

#### Mi van ha nem egy adatot küldünk hanem többet?
- a read hatására addig vár amíg meg nem érkezik hozzá a folyamat
- - észreveszi a `read`, azt, hogy nem érkezhet több adat akkor ha mindenegyes `író vég` le van zárva az alkalmazásban
=> egy `while(read)` működni fog

# A nemhaasznált végeket azonnal zárjuk le, akkor is ha nem csinál semmit!!!

### Nevesített cső
létrehozás:
````C
int fid=mkfifo("fifo.ftc", S_IRUSR|S_IWUSR );
````

SZÜLŐ
````C
fd=open("fifo.ftc",O_RDONLY); //megnyitás olvasásra
read(fd,s,sizeof(s)); //olvasás
close(fd);//bezárás
````

GYEREK
````C
fd=open("fifo.ftc",O_WRONLY);    //megynitás írásra
write(fd,"Hajra Fradi!\n",12);    //írás
close(fd);    //bezár
````
#### A szülőnek be kell várni a gyerek folyamat végét!
````C
wait(&status);
````
> A névvel ellátott csővezetékre bárki rácsatlakozhat ezért azt el kell tüntetnünk:
````C
unlink("fifo.ftc");
`````
A listázás nem mutatja a csővezeték méretét, mert az nem egy fájl hanem egy memóriában lévő valami.

##### Tegyük hatékonyabbá a csövet!
> fájl: `poll_cso.c`

````C
struct pollfd poll_fds[5]; //ilyen fájlelírókat akarunk megnézni

//                             | a fájleíró azonosítója
`````
események
`````C
poll_fds[0].fd=f;     // file decriptor
poll_fds[0].events=POLLIN;//|POLLOUT;
`````
Adatok kinyerése:
````C
int result=poll(poll_fds,1,1000); //a poll_fdsen vár 1000et
`````

A `select` hasonlóan működik mint a `poll`.


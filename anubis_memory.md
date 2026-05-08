Memory backend options for Anubis

Default configuration is in memory:
```yaml

  backend: memory
  parameters: {}
```

1. Opció Recomanada: bbolt (Escriptura en Disc)
Aquesta és la millor opció si vols persistència sense haver d'instal·lar una base de dades externa. 
Desa les dades en un fitxer de clau/valor al teu disc dur, alliberant la RAM.
Configuració:
```yaml
  backend: bbolt
  parameters:
    path: /var/lib/anubis/anubis.db 
````
 # Make sure the anubis user can write to the database

Avantatge: No consumeix RAM extra per cada sessió.
Requisit: Has de definir una clau secreta HS512_SECRET a les variables d'entorn perquè els reptes sobrevisquin als reinicis.
2. Opció Professional: valkey o redisSi ja tens un servidor Redis (o el seu fork open-source Valkey), Anubis hi pot enviar les dades.
Això és ideal per a entorns amb molt trànsit o si tens diversos nodes d'Anubis.
Com configurar-ho al teu YAML:
```yaml
store: 
  backend: valkey
  parameters:
    url: "redis://localhost:6379/0" # Adreça del teu servei Redis/Valkey
```



----
1. Com limitar la memòria amb systemdExecuta la següent comanda per editar el servei (això crearà un fitxer override):
```bash
sudo systemctl edit anubis
```

Utilitza el codi amb precaució.Dins de l'editor que s'obrirà, afegeix aquestes línies:
```ini
[Service]
MemoryMax=256M
MemoryHigh=200M
CPUWeight=50
```
```ini
# Limita la memòria RAM que pot utilitzar Anubis
MemoryMax=256M
MemoryHigh=200M
# Prioritat de CPU (perquè no "mengi" recursos al Tomcat si hi ha molta càrrega)
CPUWeight=50
Utilitza el codi amb precaució.

MemoryMax: Si arriba a aquest límit, el sistema el reiniciarà per evitar que tombi tota la màquina.
MemoryHigh: A partir d'aquí, el sistema començarà a frenar el procés per intentar no passar del límit.Després guarda i reinicia el servei:
```bash
sudo systemctl daemon-reload
```
```bash
sudo systemctl restart anubis
```
2. Evita el "heap dump" del TomcatSi el Tomcat cau quan Anubis engega, pot ser que estiguin competint pel mateix espai. Amb Anubis funcionant "fora" de la RAM (usant el backend bbolt que hem comentat abans), la pressió sobre el sistema baixarà radicalment.Assegura't de tenir això al final del teu fitxer:yamlstore:
```yaml
  backend: bbolt
  parameters:
    path: /var/lib/anubis/anubis.db
(Nota: Crea la carpeta /var/lib/anubis i dona-li permisos a l'usuari que executa Anubis abans de reiniciar).
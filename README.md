# Wprowadzenie do Kubernetesa
Repozytorium zawiera materiały z warsztatu [Stacja.IT](https://stacja.it/).

## Przygotowanie do warsztatu

Środowisko najlepiej przygotować i sprawdzić **przed** warsztatem, nie będzie to elementem spotkania.

### Mnimalne wymagania sprzętowe
* 2 CPU
* 2 GB wolnego RAM
* 20 GB wolnego dysku

### Instalacja Docker Desktop
Należy mieć zainstalowanego Dockera, najprostszym sposobem będzie zainstalowanie Docker Desktop, zgodnie z instrukcją na oficjalnej stronie.

#### Windows
https://docs.docker.com/desktop/install/windows-install/

#### Linux
https://docs.docker.com/desktop/install/linux-install/

#### MacOS
https://docs.docker.com/desktop/install/mac-install/

### Instalacja Docker Engine
Alternatywną metodą jest zainstalowanie bezpośrednio Docker Engine. Znowu należy posłużyć się oficjalną dokumentacją: https://docs.docker.com/engine/install/

### Test
Uruchomienie testowej aplikacji na Dockerze. W konsoli należy wykonać:
```
docker run hello-world
```

Rezulatat powinien być następujący:
```
[...]
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

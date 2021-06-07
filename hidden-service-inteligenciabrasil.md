## Introdução

## A importancia da privacidade
A privacidade é muito importante para a nossa segurança na internet, seja em uma navegação segura ou a necessidade que algumas pessoas precisam. Jornalistas por exemplo, eles precisam de uma segurança maior para uma conexão segura entre troca de informações ou até mesmo falar com algum contato. Profissionais de segurança da informação ou especialistas em inteligencia para não ser reconhecido e assim mantendo uma OpsSec de suas operações. E é claro o usuario normal que não quer que seus passos sejam vigiados pelas grandes corporações privadas e governamentais.

Atualmente temos uma maior vigilancia por parte dos governos, mas graças a comunidade temos mais acessibilidade a novas tecnologias seguras, por exemplo o Stop Prism que chegou para auxiliar na contra vigilancia imposta pela NSA nos Estados Unidos. Para cada solução proprietaria o Stop Prism mostra uma opensource e recomendada pela comunidade.
https://prism-break.org/pt/

## Primeiros passos com Hidden Service TOR
Durante a criação desses laboratorios vamos uma tecnologia chamada Docker para a criação dos servidores.
> Você pode ver mais informações na documentação [oficial](https://docs.docker.com/), tambem pode ver um curso de [Introdução ao Docker](https://github.com/ABase-BR/Docker-intro).

### Criação do servidor
- Instação do Docker
  - Criando servidor teste
  - Instalando TOR
  - Criando Hidden Service

### Instalação do Docker - Debian ou derivados
O Docker vai ser instalado na maquina host, podemos instalar em nosso notebook, servidor ou VPS na nuvem.

Vamos iniciar a instalação atualizando o sistema.
```sh
apt-get update
```

Vamos instalar os requisitos basicos para a instalação do Docker.
```sh
apt-get install apt-transport-https ca-certificates curl gnupg2 software-properties-common -y
```

Agora vamos obter a chave GPG e adiciona-la.
```sh
curl -fsSL https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")/gpg | sudo apt-key add -
apt-key fingerprint 0EBFCD88
```

Em seguida vamos adicionar em nosso repositorio o Docker.
```sh
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") $(lsb_release -cs) stable"
```

Vamos realizar a atualização do repositorio do nosso sistema.
```sh
apt-get update
```

Para finalizar vamos instalar o Docker e o docker-compose.
```sh
apt-get install docker-ce -y
apt-get install docker-compose -y
```

Por motivos de segurança vamos alterar a permissão do docker.
```sh
sudo groupadd docker
sudo usermod -aG docker $USER
```

Nossa instalação está completo e podemos continuar nossos testes.

### Instalação do Docker - Windows
Podemos instalar o docker no windows tambem, baixar ele no seguinte link:
```sh
https://docs.docker.com/docker-for-windows/install/
```
> Vamos baixar e instalar.

Para usar o Docker no Windows podemos usar o Power-Shell.
### Criando servidor teste
Para criar um servidor é muito simples e podemos usar diversas imagens de base. Podemos ver mais informações no [Docker Hub](https://hub.docker.com/).

Nesse exemplo vou usar uma imagem ubuntu e conseguimos ver mais imagens Ubuntu em:
```sh
https://hub.docker.com/_/ubuntu?tab=tags&page=1&ordering=last_updated
```

Para a nossa alegria a imagem Ubuntu Focal tem apenas **25.89 MB** e temos o necessario para continuar os testes.
```sh
https://hub.docker.com/layers/ubuntu/library/ubuntu/focal/images/sha256-1de4c5e2d8954bf5fa9855f8b4c9d3c3b97d1d380efe19f60f3e4107a66f5cae?context=explore
```

A maquina que vamos criar vai realizar a seguinte ação:
- docker run (Para iniciar o container).
- -it (Modo interativo)
- --rm (o container vai ser removido assim que terminar).
- ubuntu:focal (Imagem que vamos usar).
- bash (vamos interagir com o container via bash).

Para iniciar via bash podemos realizar o seguinte comando:
```sh
docker run -it --rm ubuntu:focal bash
```
> Vamos ter permissão de root usando dessa forma.

### Instalando TOR e configurando o Hidden Service
Inicialmente devemos atualizar o repositorio:
```sh
apt update
```

Vamos instalar o **TOR** e configurando o **Hidden Service** e redirecionar o apache.
```sh
apt install tor
```


Depois de instalado vamos ir até o diretório **/etc/tor/** , vamos ver que temos o arquivo **torrc**.

Por motivo de segurança vamos fazer um backup movndo o arquivo **torrc** para o **torrc-BKP**.
```sh
mv /etc/tor/torrc /etc/tor/torrc-BKP
```

> Caso esteja usando **Docker** e usar a imagem que eu dei de sugestão ela não vai ter nada instalado, nem o editor **nano** vai ser instalado.
Podemos instalar usando:
```sh
apt install nano
```

Vamos criar um novo arquivo **/etc/tor/torrc** com o editor nano, vamos realizar a modificação da seguinte forma:
```sh
nano /etc/tor/torrc
```

Com um editor de texto vamos adicionar no arquivo as seguintes linhas.
```sh
HiddenServiceDir /var/lib/tor/hidden_service/
HiddenServicePort 80 127.0.0.1:80
```

Vamos dar um restart no TOR
```sh
service tor restart
```

E vamos até o diretório **/var/lib/tor/hidden_service/**, em seguida vamos listar esse diretorio para ver os arquivos que estão disponiveis.
```sh
ls -la
```

Vamos ver que temos 2 arquivos, o principal é o **hostname**.

- **hostname** é o nome do nosso arquivo que vai ter o domínio TOR criado que esta disponível na deepweb.
> Exemplo de .onion criado: http://rkfi2yg3skhqmdjervvf5mbh4qjslitceor4juc6ssuw4n2tmnpdwuyd.onion

Agora é só acessar com o Tor Browser.

> Já vamos ter nosso endereço na rede onion, o dominio é **.onion**. Mas lembra que estamos redirecionando a porta 80 ? Vamos instalar o Apache2 só para termos um servidor funcionando na porta 80.
```sh
apt install nginx
```
Não podemos esquecer de subir o serviço:
```sh
service nginx start
```


## Acessando seu site .onion
> Temos diversas formas de acessar um site na rede onion, aqui vou explicar 3.

### Onion.ws
O projeto **onion.ws** é um gateway ou proxy darknet, para usar ele só precisamos substituir **.onion** por **.onion.ws** na barra de url do seu navegador. Ele nos auxilia no redirecionamento de sites da darknet sem a necessidade de baixar ou configurar qualquer novo software, como Tor e I2P.

> Link do projeto: https://onion.ws

Para acessar o site vamos inserir o **ws** no final.
> http://rkfi2yg3skhqmdjervvf5mbh4qjslitceor4juc6ssuw4n2tmnpdwuyd.onion.ws/

### Tor Browser
O **Tor Browser** é um navegador preparado por uma ampla comunidade que nos auxilia na privacidade e na conexão com a rede onion. Com ele podemos nos conectar em diversos dispositivos e sistemas operacionais.

Por exemplo:
- Linux
- Windows
- Android

> Veja o site oficial: https://www.torproject.org/download/

## Conclusão
Como vimos esse tipo de configuração pode ajudar estudantes a inserir projetos online de forma facil, profissionais de segurança tambem são beneficiados com a segurança a anonimato da rede e profissionais que precisam de privacidade em sua conexão.

Alem da rede TOR o projeto Stop Prism pode te dar uma lista de softwares que podem ser usados na privacidade e contra inteligencia https://prism-break.org/pt/.

Use esse conhecimento com responsabilidade, nunca deixa de compartilhar conhecimento com a comunidade e ter paciencia com quem está começando.

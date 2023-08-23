#### 21/08/2023

CURSO NGINX:
servidor Web, Proxy Reverso e API Gateway

sudo lsof -i:8080
kill -9

brew services start jenkins-lts
brew services stop jenkins-lts
brew services restart jenkins-lts

@01-Conhecendo a ferramenta

@@01
Apresentação

[00:00] E aí, pessoal? Boas-vindas à Alura. Sou o Vinicius Dias e vou guiar vocês nesse nosso primeiro treinamento sobre NGINX. Antes de qualquer coisa nós vamos aprender o que é esse tal de NGINX, para quê ele serve, como ele funciona e quais problemas ele resolve.
[00:16] E a partir dessa base teórica, entendendo qual o lugar do NGINX na web (afinal de contas ele é um servidor web), nós vamos começar a botar a mão na massa.

[00:25] E quando começarmos a botar os conhecimentos em prática, nós vamos - obviamente depois de instalar o NGINX em cada uma das plataformas - configurar um servidor web. Esse servidor web vai saber responder diretamente a arquivos estáticos, sem passar por nenhuma outra aplicação.

[00:40] Só que às vezes nós precisamos servir aplicações dinâmicas usando linguagens como PHP, Java, C Sharp, Python, Ruby, ou qualquer outra. Então nós vamos entender conceitos que nos permitem receber as requisições no NGINX e, caso necessário, vamos passar essas requisições para algum servidor de aplicação.

[00:59] A partir disso, nós vamos entender conceitos como API Gateway, nós vamos aprender a fazer um load balancer e, enquanto implementamos esses detalhes, veremos alguns problemas que temos que atacar, como: configuração de logs, modificação do formato de logs e adicionar informações a esses logs padrões.

[01:16] Então vamos ter bastante coisas para brincarmos. O melhor de tudo é que é de forma super simplificada! Você não precisa conhecer nenhuma linguagem de programação para realizar este treinamento. Tudo o que você precisa é conhecer o protocolo HTTP. Então se você fizer o treinamento de HTTP antes desse aqui, você já tem todos os conceitos e conhecimentos necessários.

[01:37] Se você já sabe programar, e se você conhece alguma linguagem de programação a ponto de saber levantar um servidor de aplicação, perfeito! Melhor ainda. Porque você vai visualizar de forma mais prática como isso se aplica ao seu cenário - mas caso contrário, não tem problema nenhum porque não vamos precisar disso nesse treinamento.

[01:56] A todo momento eu vou te informar: “olhe só, esse cenário aqui seria aplicável para nós utilizarmos um servidor de aplicação. Esse cenário, não precisamos de um servidor de aplicação”.

[02:07] E dessa forma, quando você se deparar com uma demanda real - por exemplo: fazer deploy de um servidor Python, fazer deploy de uma aplicação PHP - aí você vai saber pesquisar as especificidades. "Como realizar proxy reverso para um servidor Python", por exemplo.

[02:21] Então, dessa forma você vai ter todas as ferramentas necessárias para colocar uma aplicação web em produção. Obviamente, para isso você vai precisar ter uma aplicação. Então nesse ponto entra a programação, mas a parte de infraestrutura de um servidor web esse treinamento vai te prover.

[02:37] Espero que você tire bastante proveito e que você aprenda muitas coisas. Já te adianto que não vamos conseguir cobrir tudo que o NGINX faz em um só treinamento, mas vamos aprender bastante coisas.

[02:47] E se nesse amontoado de informações você ficar com alguma dúvida, não hesite. Você pode abrir um tópico lá no fórum. Eu tento responder pessoalmente sempre que possível, mas quando eu não consigo, nós temos uma vasta comunidade de alunos, moderadores, instrutores e com certeza alguém vai conseguir te ajudar.

[03:06] Mas já falei bastante, vamos finalmente partir para a "prática" e conhecer o que é esse tal de NGINX! Vamos entender o que é, o que ele faz e como ele se encaixa na web no próximo vídeo.

@@02
Prazer, NGINX

[00:00] Antes de instalarmos, configurarmos e utilizarmos o NGINX, antes de realmente conhecermos a parte prática, nós vamos entender um pouco da teoria. O que um NGINX faz e como ele faz. Então, vamos lá!
[00:15] O NGINX é um servidor web. Ele é um serviço, é um programa que roda e que serve para responder requisições web. Então, é muito importante que você entenda como a web funciona. Por isso, o pré-requisito deste treinamento é o curso de HTTP. Você precisa entender o protocolo HTTP para saber qual é o papel do NGINX.

[00:34] Então super simplificando milhares de conceitos que você já deve ter aprendido no curso de HTTP, a web funciona com uma arquitetura cliente-servidor. Temos aqui o cliente e o servidor, então imagine que o cliente é um navegador web; e nós temos um servidor rodando, uma aplicação em Java, em PHP, C Sharp, Python, Ruby ou a linguagem que for.

[00:55] Então, o cliente vai digitar a URL do nosso site: “alura.com.br”, por exemplo, e ele vai fazer uma requisição para o computador que tem o nome “alura.com.br”.

[01:08] Esse computador precisa saber receber essa requisição web e fazer o que tem que fazer - ou exibir o HTML, a imagem, devolver o arquivo CSS - ou então mandar para uma aplicação processar essa requisição. Uma aplicação em Java, em PHP, em Python ou no que for.

[01:25] Essa tarefa de receber a requisição e decidir o que vai fazer, se vai mandar para uma aplicação, se vai devolver algum arquivo direto - essa é a tarefa de um servidor web.

[01:37] Então é aí que entra o NGINX! Esse é o papel dele. O que o NGINX vai fazer dentro de um servidor é ficar ouvindo uma porta. Quando falamos de HTTP, a porta padrão é a “80”. Então se você não digita uma porta e está utilizando HTTP, essa requisição vai cair na porta 80.

[01:57] Então um servidor web fica lá ouvindo, por exemplo, a porta 80 e ele fica esperando alguma requisição ou algum pedido entrar pedido chegar nesse computador. Quando chega, esse servidor web tem que saber o que fazer.

[02:13] Então, ele vai analisar. Se o que você está pedindo parece ser um arquivo de imagem, então não preciso fazer nada com mais ninguém. Eu só vou te devolver essa imagem e o navegador vai exibir para você.

[02:23] Você está me pedindo um arquivo estático, um arquivo de CSS? Então só vou te devolver, não preciso envolver ninguém nesse processo.

[02:31] Agora, o que você está me pedindo aqui parece ser uma rota, uma URL, que precisa ser executada por uma aplicação, em PHP, Python ou Java.

[02:41] Então o que ele faz é poder transferir a responsabilidade para algum servidor de aplicação. Esse servidor de aplicação pode estar, por exemplo, aberto ouvindo uma outra porta.

[02:54] E esse servidor web manda uma outra requisição (não utilizando o protocolo HTTP, necessariamente, pode ser usando outro formato), mas ele manda esse pedido para o servidor de aplicação.

[03:04] O servidor de aplicação processa, devolve para o servidor web, e o servidor web vai devolver para o cliente o que ele recebeu. Essa comunicação entre um servidor web e um servidor de aplicação pode ser feita de diversas formas. Então vamos focar, pelo menos no início, só nessa tarefa individual do servidor web tratando o que ele tem que tratar, de forma isolada.

[03:28] Agora, caso você já seja uma pessoa que desenvolve sistemas e que você já conhece alguma linguagem, você já sabe como fazer um servidor de aplicação funcionar.

[03:38] Então, por exemplo: com Java você pode ter um Tomcat rodando, ou com PHP você vai ter um PHP FPM rodando... Enfim. Você vai ter algum servidor de aplicação aqui, e o NGINX consegue se conectar à ele. Mas nós vamos focar só na parte do NGINX, sem nos conectarmos a algum servidor de aplicação.

[03:55] O NGINX é um servidor web, ele ouve uma porta para responder requisições HTTP - mas já existiam outros servidores que fazem exatamente isso. Acredito que o mais famoso seja o Apache, muito provavelmente você já ouviu falar do servidor HTTP da Apache.

[04:11] Qual é a grande diferença do NGINX? Simplificando também, a ideia do Apache era: você tem um servidor do Apache rodando processo do Apache. Quando chega uma requisição, esse processo cria um novo processo e trata essa requisição, depois mata esse processo e fica tudo certo.

[04:32] Só que isso é muito custoso. Criar um processo, fazer a mudança de contexto de um processo para o outro e depois matar um processo. Isso é uma tarefa custosa. Então, o que o NGINX faz?

[04:43] Ele não usa somente essa ideia de processos, threads e programação paralela, ele usa um outro conceito de programação assíncrona muito interessante - que eu vou simplificar aqui também para você entender o conceito por alto.

[04:56] Então, temos aqui uma base do funcionamento do NGINX. Quando você inicia um serviço do NGINX, ele cria um processo principal como se fosse o patrão desses colaboradores aqui.

[05:12] Então esse patrão vai criar alguns processos colaboradores, alguns worker process. Esses worker process são criados baseado no número de núcleos que o seu processador tem.

[05:29] Suponha que eu tenho um servidor que tem um processador com quatro núcleos. Então eu poderia criar, por exemplo, 4 processos worker process para tratar requisições. Porque assim eu tenho um maior número de processos tratando cada número de requisições, então eu consigo tratar mais requisições.

[05:49] Só que a sacada é: não é como se cada processo tratasse uma requisição. Não é isso! Cada um desses processos trata um número grande de requisições, várias requisições, e para que ele consiga tratar mais de um requisição, ele usa um conceito de Multiplexing I/O. Ou seja, ele faz mais de uma coisa de forma assíncrona.

[06:13] Então imagine que chegou uma requisição nesse worker aqui e depois chega uma nova requisição que caiu nesse mesmo worker. O que ele vai fazer?

[06:21] Ele vai colocar para executar o primeiro. Enquanto esse processo está esperando essa tarefa ser executada - por exemplo: o servidor de aplicação responder e o arquivo ser carregado - ele já trata outra requisição, coloca para carregar imagem e manda requisição para servidor de imagem.

[06:38] Depois ele pega a resposta da primeira requisição e devolve, continua tratando a segunda e devolve. Por isso o NGINX veio com a proposta de ser o servidor web mais rápido.

[06:49] Existem benchmarks que colocam realmente o NGINX lá no topo, ou seja, é um servidor muito performático. Mas obviamente não existe bala de prata e ele tem seus cenários onde ele trabalha muito bem e não é ferramenta ideal.

[07:04] Vamos entender melhor como ele funciona durante esse treinamento, mas a base da funcionalidade dele é essa. Ele inicia um servidor, um processo. Esse processo cria outros processos colaboradores, e cada um desses processos consegue tratar várias requisições.

[07:21] Utilizando esse conceito de programação assíncrona, ele consegue ter uma grande performance. Eu falei bastante da teoria, só que agora nós vamos partir para a prática. Vamos finalmente instalar o NGINX na nossa máquina e ver o que ele pode fazer por nós, no próximo vídeo!

@@03
Papel do servidor

Aprendemos algumas coisas neste vídeo. O que é um servidor web, como o nginx funciona, etc.
Mas na prática, via de regra, qual o papel de um servidor web?

Ouvir conexões udp em alguma porta configurada
 
Alternativa correta
Ouvir conexões tcp em alguma porta configurada
 
Alternativa correta! Um servidor web vai ficar esperando uma conexão chegar. Quando ela chegar, o servidor web faz seu trabalho, garantindo que a mensagem recebida está no formato HTTP e depois fazendo o que deve fazer segundo suas configurações.
Alternativa correta
Ouvir conexões tcp na porta 80
 
Alternativa errada! Nem só de porta 80 vive a web. A porta pode ser configurada.

@@04
Instalação

[00:00] Vamos falar sobre como nós podemos instalar o NGINX na nossa máquina.
[00:06] Existem várias e várias formas de termos o NGINX rodando no nosso computador. Uma delas é, por exemplo, você utilizar containers. Então se você já conhece Docker, eu recomendo que você tente realizar essas etapas dentro de containers do Docker.

[00:21] Mas para não ter esse conhecimento como pré-requisito, eu vou instalar diretamente na minha máquina.

[00:26] Então se você utiliza Linux, você pode simplesmente executar, por exemplo, o comando sudo apt install nginx. Ou utilizando o gerenciador de pacotes da sua distribuição Linux ao invés de apt.

[00:40] Se você está no Mac, você pode utilizar o Homebrew para instalar, então você pode digitar o comando brew install nginx. Então um comando, NGINX instalado.

[00:51] Já para quem utiliza o Windows, como essa é uma ferramenta de gestão de servidores e Windows não é tão usado em servidores web, nós vamos ter um trabalho a mais.

[01:04] Vamos acessar o site https://www.nginx.org, que é o site oficial desse servidor web de código aberto. Você vai vir aqui no canto inferior direito em “download”. Aqui você tem, na mainline version a versão 1.19.9, que é a versão atual do momento da gravação desse curso.

[01:21] Você pode utilizar qualquer versão que comece com o número 1. Se no momento que você estiver assistindo for 2 ponto alguma coisa, então o ideal é você pegar uma versão um pouco mais antiga, para garantir que as configurações que estamos fazendo aqui continuem funcionando.

[01:35] Então você vai baixar esse arquivo que vai ser um ZIP, e você vai extrair esse ZIP em um caminho que seja de fácil acesso através do terminal. Idealmente um caminho que não tenha espaço no nome, porque o Windows costuma trazer bastantes problemas nesse cenário.

[01:50] Então, por exemplo: dentro do seu disco “C:” você cria uma pasta “Servidor” ou “Server”, como você quiser. Você vai extrair desse ZIP e dentro dessa pasta vai ter o executável do NGINX. Ali dentro estarão os arquivos de configuração também.

[02:09] Vamos falar bem mais sobre arquivo de configuração, mas feito esse processo de extração desse ZIP, você vai ter acesso ao comando NGINX desde que você esteja nessa pasta.

[02:21] Então imagine que você extraiu em “C:\Servidor\NGINX”. Então você vai acessar com “cd”, que é change directory, ou seja, mudar o diretório. Você vai acessar a pasta pelo comando cd C:\servidor\nginx.

[02:40] E acessada essa pasta, você vai digitar simplesmente nginx. Isso aqui é o suficiente para você inicializar o servidor. No meu caso ele deu um monte de erros, porque eu já tenho o servidor do NGINX rodando.

[02:53] Então repare que ele mostrou aqui uma porta, que é a porta que vem configurada por padrão. Nós vamos falar bastante sobre arquivo de configuração, mas por padrão o nosso servidor vai subir na “8080”.

[03:06] Tendo visto isso daqui, então digitei o comando nginx no Linux, no Mac ou no Windows - nós temos um servidor rodando. Então, o que eu vou fazer? Aqui no meu navegador eu vou digitar https://localhost:8080. Agora temos uma mensagem: “bem-vindo ao NGINX”, e dessa forma nós temos o nosso servidor web instalado.

[03:29] E o que aconteceu aqui para essa mensagem ser exibida? Temos um arquivo HTML que tem essa mensagem de bem-vindo e outras coisas; e nós fizemos uma requisição HTTP para a nossa própria máquina usando essa porta.

[03:44] Então o NGINX está ouvindo essa porta aqui e ele está configurado para responder com esse HTML, ou seja, o nosso servidor web já está funcionando.

[03:55] Claro, o que queremos fazer é colocar alguma aplicação real para ser exibida aqui, então não queremos ver somente a mensagem padrão do NGINX. Então a partir do próximo capítulo vamos começar a conhecer os arquivos de configuração do NGINX.

@@05
Localhost

Vimos no final do vídeo que ao acessar o endereço http://localhost nós vemos a página HTML configurada pelo nginx por padrão.
Mas como o nginx e o computador sabem o que significa "localhost"?

Através de uma configuração inacessível do sistema operacional
 
Alternativa correta
Através do arquivo de hosts
 
Alternativa correta! Todo sistema operacional possui um arquivo de hosts. No Linux, por padrão fica em /etc/hosts. No Mac, /private/etc/hosts. Já no Windows, C:\Windows\System32\Drivers\Etc\hosts. Esse arquivo informa ao sistema operacional que quando uma conexão for estabelecida usando algum nome, o IP correspondente deve ser usado. Para o nome localhost, temos o IP da nossa própria máquina (127.0.0.1).
Alternativa correta
Através de um servidor padrão que o nginx cria chamado localhost
 
Alternativa errada! O computador precisa saber que esse nome significa nossa máquina local, independente do nginx.

@@06
Faça como eu fiz

Chegou a hora de você seguir todos os passos realizados por mim durante esta aula. Caso já tenha feito, excelente. Se ainda não, é importante que você execute o que foi visto nos vídeos para poder continuar com a próxima aula.

Continue com os seus estudos, e se houver dúvidas, não hesite em recorrer ao nosso fórum!

@@07
O que aprendemos?

Nesta aula, aprendemos:
Aprendemos o que é Nginx
Vimos o propósito de um servidor web
Aprendemos a instalar o Nginx
Entendemos como o Nginx funciona

#### 22/08/2023

@02-Servidor HTTP

@@01
Arquivos de configuração

[00:00] E aí, pessoal? Boas-vindas de volta a mais um capítulo desse treinamento, onde vamos começar a brincar um pouco com o NGINX. Vamos entender como funciona um servidor web.
[00:09] E como eu comentei, isso aqui já é um servidor web rodando, ele já está nos respondendo de forma válida. Então vamos entender o que ele fez e o que ele já tem por padrão.

[00:20] Então vou vir aqui no meu terminal e eu vou digitar o comando nginx -h. O que isso vai nos mostrar? Vai nos mostrar a ajuda. Ele mostra primeiro a versão que eu estou utilizando, o uso que eu posso fazer aqui e todas as opções que eu posso passar.

[00:37] Eu tenho algumas informações interessantes aqui. Primeiro: ele mostra no meu sistema operacional qual é o caminho de prefixo, o caminho onde tem as configurações e onde tem o que eu preciso conhecer.

[00:50] Então repare aqui, dentro desse arquivo eu devo ter provavelmente aqueles nossos arquivos que estam gerando essa página aqui. Posso ter mais arquivo de configuração etc.

[01:04] Eu tenho aqui arquivos de log, para onde as coisas vão ser salvas. Eu tenho arquivos de configuração padrão, então repare que aqui eu já vejo um arquivo de configuração padrão. Então acho que esse é um lugar interessante para nós começarmos.

[01:19] No seu computador esse caminho pode ser diferente. Então, através dessa informação é que vamos entender o que vamos analisar. Você vai abrir esse arquivo padrão em qualquer editor de texto. Eu aqui vou abrir no ‘vim’ para não precisar sair do terminal.

[01:35] Então vou digitar o comando vim /usr/loca/etc/nginx/nginx.conf. Então vamos entender o que está acontecendo. Aqui você vai reparar que tem um monte de linhas que começam com cerquilha (#). Isso significa que essas linhas são ignoradas, elas não têm valor, são somente comentários.

[01:57] Então para nós visualizarmos isso um pouco melhor aqui no meu computador, eu vou fazer um outro comando para ler esse arquivo sem esses comentários. Mas você pode manter seu editor de texto aberto que vamos editar por lá.

[02:09] Então já tem os comando aqui e essas são as configurações realmente carregadas, sem espaço em branco, sem os comentários etc.

[02:17] Então repare que temos bastante coisas e podemos até ficar perdidos em algumas coisas. Eu tenho aqui o número de ‘worker_processes’, então aqui por padrão ele está colocando só ‘1’ para mim.

[02:31] Eu posso mudar essa configuração para ‘auto’, e provavelmente na sua instalação de Linux isso já veio como ‘auto’, mas eu vou manter esse ‘1’ porque nós não vamos tratar de performance nesse treinamento.

[02:43] Aqui ele nos diz quantas requisições e conexões o mesmo ‘worker’ pode receber. Isso aqui é o limite que um processo pode ter de file descriptors. É o limite que um processo pode ter, no meu sistema operacional, de coisas assíncronas, vamos chamar assim.

[03:01] Então no Linux e em sistemas Unix no geral, tudo é considerado um arquivo. Então se eu tenho socket, se eu tenho uma pasta ou um processo, tudo pode ser visto como um arquivo. Então isso aqui é o número de file descriptors, ou seja, descritores de arquivo; para cada processo poder trabalhar.

[03:19] Então podem ser socket, podem ser conexões usando outra forma ou alguma outra técnica, mas no final das contas esse é o número de conexões que eu posso ter. Eu posso mexer no meu sistema operacional para aumentar isso - mas de novo, não vou mexer por agora.

[03:33] E agora entramos na parte de configuração do HTTP propriamente dito. Então, o que nós temos aqui? Ele faz o ‘include’ de outro arquivo, não vou me preocupar com isso. Ele coloca aqui que se nenhum tipo for definido na resposta, ele vai colocar esse tipo aqui. Não vamos nos preocupar com isso. Passa outras informações, mas esse é o ponto principal, é onde ele configura o nosso servidor web.

[03:57] Então repare que ele está ouvindo a porta 8080, em um nome de servidor ‘localhost’.

[04:03] Se tivéssemos algum host configurado na nossa máquina, ou seja, no arquivo de hosts do sistema operacional. Se disséssemos ‘alura.com.br’ iria ser direcionado para nossa máquina. Então aqui se colocássemos ‘alura.com.br’, ele conseguiria pegar essa requisição e tratar diretamente aqui no nosso servidor.

[04:24] Não vamos mudar isso, vamos utilizar ‘localhost’, mas em um servidor real aqui no server_name você coloca o nome do domínio do seu site.

[04:34] Aqui em 'location' vem a configuração que indica o que fazer quando ele receber alguma requisição. Se essa requisição for para ‘/’, ou seja, para alguma coisa que não tem caminho nenhum, então ele vai buscar dentro da pasta HTML o que nós informarmos.

[04:51] E se nós não informarmos nada, se realmente a requisição for só ‘/’ e não ‘/ mais alguma coisa’, caso não informemos nada o que ele vai fazer é buscar um arquivo de índice, um arquivo principal que pode se chamar ‘index.html’ ou 'index. htm'.

[05:08] Então, com isso, o que eu tenho de informação? Eu sei que dentro da pasta HTML que está naquela pasta de prefixo que nós vimos, eu preciso ter um arquivo ‘index.html’ que vai ter esse conteúdo aqui da página de boas-vindas.

[05:22] Então, vamos testar isso? Vamos ver de novo qual é aquele caminho - que é isso daqui: '/usr/local/Cellar/nginx/1.19.8/'.

[05:28] Então aqui dentro eu espero ter uma pasta HTML e eu espero que aqui dentro eu tenha um arquivo ‘index’. Então vamos abrir esse arquivo ‘index’ e eu vou abrir com o nosso ‘vim’.

[05:40] Aqui, ao invés de botar ‘Welcome to nginx’, eu vou adicionar aqui nesse título <h1>Welcome to nginx (Teste)!</h1>.

[05:48] Então eu alterei esse conteúdo, salvei e vou atualizar o navegador.

[05:54] Então eu já entendi o que está acontecendo por trás dessa nossa configuração.

[05:58] Agora, um último detalhe é que via de regra, o que vamos fazer é não editar esse arquivo - e eu acredito que no Linux você nem vai ter esse server aqui no arquivo principal.

[06:13] Esse arquivo só vamos editar em cenários de tuning, de otimização e esse tipo de coisa. Mas para configurarmos realmente um servidor, nós vamos acessar essa pasta aqui de ‘servers’.

[06:24] Então aqui dentro nós vamos criar arquivos de configuração específicos para cada servidor que quisermos. Então o que eu vou fazer é criar um novo servidor HTTP, que ao invés de ouvir a porta 8080, vai ouvir a porta padrão HTTP: a porta 80.

[06:40] Se no seu computador esse servidor padrão que o NGINX traz estiver ouvindo a porta 80 então você vai fazer o contrário. Nós vamos criar um novo servidor que ouça a porta 8080.

[06:51] Então nós vamos criar um novo servidor que ouça uma porta diferente e que vá carregar algum outro arquivo que iremos decidir ainda. Só que isso tudo iremos fazer a partir do próximo vídeo.

@@02
Caminhos dos arquivos

Vimos neste vídeo que há mais de um local onde vamos configurar arquivos do nginx.
Como saber onde está o arquivo de configuração principal?

Gravando o caminho em cada sistema operacional
 
Alternativa correta
Através do comando nginx -t
 
Alternativa correta
Através do utilitário da CLI nginx
 
Alternativa correta! Este comando nos fornece diversas informações. Ao digitarmos nginx -h uma ajuda é exibida, e lá podemos ver o caminho do arquivo de configuração.

@@03
Criando um servidor

[00:00] Vamos finalmente configurar do zero um servidor nosso, não vamos mais utilizar esse servidor aqui que está todo configurado.
[00:09] Então, como vamos fazer isso? Se você estiver no Linux, ao invés de ter esse server aqui, muito provavelmente só tem um include de um outro lugar, de um outro arquivo - e muito provavelmente esse outro lugar tem um arquivo chamado "default.conf".

[00:25] Dependendo da sua instalação, você pode ter pastas conhecidas como sites enabled e sites available, ou somente esse arquivo default.conf. Mas você vai ter todas essas informações dando uma olhada nesse arquivo aqui, 'includ server/', onde você tem os includes.

[00:41] Então dentro dessa pasta que tem o include é que vamos criar um novo arquivo, ou caso você já tenha o default.conf, você pode utilizar ele. Inclusive, eu vou até utilizar esse mesmo nome default.conf para termos algo parecido.

[00:55] Então, eu preciso achar essa pasta servers e esse meu arquivo de configuração está nesse caminho, ‘/usr/local/etc/nginx/’.

[01:01] Então, eu espero que nesse caminho tenha uma pasta servers. Vamos ver aqui. Eu vou utilizar o ‘vim’ para editar algum arquivo e quando eu começo a digitar ‘servers’ e pressiono a tecla “Tab”. Ele completou, então essa pasta existe.

[01:13] Então vou criar um arquivo default.conf. É um arquivo vazio, repare que ele está mostrando a mensagem ‘New File’.

[01:19] Então vamos começar a editar. Eu vou criar um novo servidor server {}.Vou colocar dentro das chaves o que eu quero com esse servidor agora, que é listen 80; ou seja, eu quero ouvir a porta 80, que é a porta padrão do HTTP.

[01:32] De novo, se na sua instalação aquele servidor padrão já veio na porta 80, então aqui você vai colocar ‘8080’ - porque isso depende muito do sistema operacional, da versão etc. Só para irmos alternando.

[01:48] Estou ouvindo a porta 80 aqui e eu vou colocar server_name localhost; porque eu estou acessando a minha própria máquina e eu não tenho nenhum outro nome para minha máquina, só ‘localhost’.

[02:00] Se eu não colocar nenhum server name eu posso acessar através do IP da minha máquina, ou ‘127001’ ou “000”, enfim - mas eu vou dar um nome aqui para continuarmos utilizando o ‘localhost’.

[02:12] Tenho o servidor configurado. Agora eu quero informar para ele: “quando chegar alguma requisição para qualquer lugar, você vai tentar buscar os arquivos em alguma pasta”.

[02:24] Quando chegar uma requisição em location /, ou seja, a partir de qualquer lugar e aqui nessa ‘location’ nós podemos fazer bastante coisas. Eu posso ter alguma rejects alguma coisa; eu posso dizer que quando chegar ‘/alguma-coisa’ e qualquer resto que vier, eu vou ter essa configuração aqui.

[02:46] Mas no meu caso, como eu coloco só barra (/), significa que qualquer requisição que chegar nesse servidor, vai entrar nesse bloco.

[02:55] Então fica aí uma dica, bem para o futuro que iremos trabalhar. Posteriormente eu poderia, por exemplo, ver se essa ‘location’ é um arquivo PHP. Se for um arquivo PHP, mando isso para outro lugar, para o servidor de aplicação. Mas enfim, vamos focar no que temos aqui.

[03:11] Sempre que chegar uma requisição para qualquer lugar, eu vou definir essas configurações. Qual vai ser a raiz dos arquivos desse meu servidor? Qual vai ser o root? Então, eu vou colocar isso como uma pasta do meu usuário. Você vai colocar uma pasta que você preferir. De novo, a recomendação é que não tenha nenhum caminho com espaços.

[03:32] Então no meu caso, eu vou adicionar nessa pasta root /Users/vinicius.dias/Dev/nginx;. Então aqui dentro eu vou ter os meus arquivos HTML, de imagem etc.

[03:45] Repare que eu estou usando o caminho completo. Já naquele servidor, naquele arquivo de configuração padrão, naquele include, ele tava passando um caminho relativo. Eu estou passando o caminho absoluto porque eu não quero meus arquivos aqui dentro da pasta ‘server’.

[04:01] Então eu vou botar em outro lugar completamente diferente, para isso eu coloco o caminho completo. Se você está no Linux o padrão também é desse mesmo formato. Se você está no Windows, sempre comece com ‘C:’ ou ‘D:’ - o nome do seu disco.

[04:16] Defini o root do meu servidor, mas e se eu acessar simplesmente ‘localhost’? Fizer isso sem colocar o nome de algum arquivo? Eu posso definir um arquivo padrão que vai ser carregado através do ‘index’.

[04:29] Então vou dar o nome deste arquivo de index.html, igual nós já tínhamos lá. Teoricamente configurado, agora se eu vir aqui no meu navegador e acessar https://localhost:80 ou eu posso até omitir esse ‘80’ (porque é a porta padrão HTTP), eu espero receber alguma coisa tentando acessar aquele arquivo, ou algo do tipo, um ‘404’ou algo assim.

[04:57] Mas eu não consegui acessar, ele está recusando a conexão. Por que será que isso está acontecendo? Será que nosso arquivo de configuração está errado? Será que faltou alguma coisa? Vamos entender um pouco melhor esse processo no próximo vídeo.

@@04
Entendendo a location

Finalmente configuramos um servidor web nosso, usando a diretiva server e dentro dela temos a diretiva chamada location.
Qual o propósito dessa diretiva location na configuração de um servidor web no nginx?


Alternativa correta
Informar qual caminho acessado cairá nas regras a seguir
 
Alternativa correta! Se definirmos um location /, tudo que começar com / (que no caso é literalmente tudo) cairá nesse conjunto de regras. Nessas regras podemos definir qual o diretório raiz do projeto, qual o arquivo padrão, regras de redirecionamento, etc.
Alternativa correta
Informar o caminho raiz do nosso site / sistema
 
Alternativa errada! Dentro de location podemos definir mais coisas além de root.
Alternativa correta
Informar o arquivo padrão a ser carregado em nosso site / sistema
 
Alternativa errada! Dentro de location podemos definir mais coisas além de index.

@@05
Sinais e comandos

[00:00] Nós tentamos configurar o nosso servidor, mas teve um problema aqui de conexão recusada. Então vamos entender o que aconteceu.
[00:10] Quando eu inicializei o meu servidor web,o meu NGINX, ele leu aquele ‘nginx.conf’, aquele arquivo padrão, carregou tudo o que tinha para carregar e pronto, subiu o nosso servidor.

[00:21] Eu fui lá, eu criei um arquivo de configuração, só que o NGINX não está nem aí para mim porque ele já carregou todas as configurações que tinha que carregar. Então eu preciso informar: “olhe só, NGINX, eu mudei alguma coisa. Eu preciso que você recarregue as configurações”. Então, para isso vamos aprender mais um comando aqui do NGINX.

[00:42] Deixe-me limpar a tela e usar o comando nginx -h para nós vermos aquela ajuda. Com esse -s nós podemos informar sinais para o processo principal. Podemos parar o processo; podemos pedir para ele sair, nós podemos reabrir o processo ou, exatamente o que nós queremos, recarregar as configurações desses processos.

[01:05] Então tudo o que precisamos fazer aqui é digitar o comando nginx -s reload. Teoricamente está tudo certo, ele recarregou as configurações. Agora, se eu tentar acessar pelo navegador nós temos o “404 Not Found” - que é exatamente o que eu esperava, porque nós não criamos o arquivo onde ele está tentando buscar o nosso ‘root’.

[01:23] Então, o que nós tivemos aqui? Nós passamos um sinal para o serviço do NGINX. Nós podemos também fazer algumas outras coisas. Por exemplo: podemos tentar verificar se nossos arquivos de configuração estão corretos. Então se eu faço o comando nginx -t, ele verifica que a sintaxe está OK, então o teste deu tudo certo e o nosso arquivo de configuração está beleza.

[01:47] Se eu tentar acessar aquele nosso arquivo de configuração e tirar um ponto e vírgula, por exemplo, salvar e fazer o comando nginx -t - aí ele já nos avisa que esse arquivo tem um erro. Então vamos corrigir o erro e adicionar o ponto e vírgula. Então dessa forma conseguimos ver se está tudo OK com o arquivo e conseguimos recarregar as configurações.

[02:10] Vamos de novo dar uma olhada aqui no arquivo 'default.conf' e criar essa pasta dentro do meu usuário. Em ‘Dev’ eu quero criar uma pasta ‘nginx’ e ter o nosso arquivo ‘index.html’. Deixe-me sair daqui.

[02:25] Aqui, no terminal, eu já estou na pasta do meu usuário, então dentro de ‘Dev’ eu quero criar ‘nginx’. Agora dentro dessa pasta ‘nginx’, eu vou criar o nosso ‘index.html’, então vou simplesmente escrever aqui ‘Arquivo index’.

[02:39] Então você vai, na pasta onde você definiu como ‘root’, criar o arquivo ‘index.html’ e botar qualquer conteúdo que você quiser. Pode escrever um HTML válido, ou no meu caso aqui, como sou preguiçoso, só uma mensagem. Salvei e posso vir no navegador e carregar nosso arquivo “index”.

[02:55] “Vinicius, por que nesse cenário você não precisou recarregar as configurações?” Porque isso que eu fiz não foi mudar nenhuma configuração. Eu mudei um arquivo que o NGINX vai tentar acessar quando ele recebe a requisição. Então ele recebeu a requisição e agora o arquivo existe. Isso não é uma configuração.

[03:12] E se eu tiver um arquivo diferente de ‘index’, por exemplo? Se eu tiver aqui um ‘teste.html’, então escrevo ‘Arquivo teste’. Como será que eu posso acessar ele? Lá no nosso ‘location’ eu estou dizendo que barra (‘/’) vai acessar “index”.

[03:28] Só que na verdade, como eu já comentei, isso aqui significa que sempre que chegar uma requisição para barra e qualquer coisa mais para frente, ele vai tentar buscar aqui nesse ‘root’.

[03:38] Então se eu digitar aqui na barra de endereço do navegador, além do barra, o ‘teste.html’: https://localhost:80/teste.html ele vai acessar o nosso arquivo teste. Agora, se eu acessar alguma coisa que não existe - vou digitar ‘alguma-coisa’ - ele vai tentar encontrar esse arquivo ‘alguma-coisa’ e vai dar um “404”.

[03:54] Só que essa mensagem “404”, espero que você concorde comigo, não está muito bonita. Além de não ser a mais bonita possível, ela está nos trazendo informações do nosso servidor: "nginx/1.19.8".

[04:06] É interessante não expor esse tipo de informação para que atacantes não tenham esse tipo de informação, porque assim ele pode buscar as vulnerabilidades conhecidas nessa versão e tentar explorar isso.

[04:17] Então o que queremos fazer para o nosso site é criar uma página de “404” bonita ou uma página geral de erros mais bonita. Então vamos entender como podemos configurar páginas de erro usando o NGINX no próximo vídeo.

@@06
Páginas de erro

[00:00] Infelizmente quando desenvolvemos uma aplicação, um site ou um sistema, erros acontecem. Quando estamos falando de HTTP, erros são representados através de códigos HTTP. De novo, por isso é importante fazermos o treinamento de HTTP, para entendermos melhor como isso funciona, mas eu vou super simplificar um conceito aqui.
[00:22] Se aconteceu algum erro e estamos utilizando bem o HTTP, um código de resposta é devolvido. Então, o código de erro “404” indica que algum recurso que eu estou tentando acessar não foi encontrado.

[00:39] O código “200” indica que está tudo OK, que nós encontramos o que tinha que encontrar e processamos o que tinha que processar. Então existem diversos códigos HTTP.

[00:50] Os códigos de erro estão na faixa “400” quando é algum erro do cliente, ou seja, o cliente digitou a URL errada, por exemplo. Ou na faixa “500” quando é erro no servidor. Então, por exemplo: o servidor não está disponível, o servidor de aplicação caiu, meu banco de dados está fora ou alguma coisa assim.

[01:07] Então vamos configurar exatamente o erro “404” para ser direcionado para uma página nossa, uma página que nós podemos configurar como quisermos, deixar ela bonita etc.

[01:17] Então, vamos lá! Já estou aqui com meu arquivo de configuração aberto e fora de location eu vou adicionar o comando error_page - pode ser antes ou depois, tanto faz.

[01:28] E aqui, o que eu posso informar? Os códigos de erro, por exemplo: “404”, “400”, “401” etc. - e isso vai ser direcionado para ‘/erro.html’, error_page 404 400 401 /erro.html.

[01:42] Então, o que vai acontecer aqui? Algum desses erros (404, 400 ou 401) aconteceu, então o NGINX vai carregar como se ele estivesse acessando uma nova requisição HTTP para ‘/erro.html’. Então, o que eu espero? Que dentro desse ‘root’ aqui eu tenha esse arquivo ‘erro.html’.

[02:02] Então vamos salvar esse arquivo de configuração. Primeiro, eu vou garantir que está tudo certo e OK, se a sintaxe está certa. Então eu vou lá no meu ‘root’, que é em ‘vim Dev/nginx/error.html’. Eu vou escrever aqui "mensagem de erro", ou "código de erro", e vou poder fazer o que eu quiser aqui.

[02:20] De novo, se eu tento acessar pelo navegador alguma coisa não encontrada, ele não vai carregar aquele arquivo, porque precisamos recarregar as configurações. Então eu digito o comando nginx -s reload. Configurações recarregadas quando eu atualizo... "Mensagem de erro". Por quê?

[02:36] Nós tentamos acessar ‘/alguma-coisa’. Esse ‘/alguma-coisa’ não existe, então o NGINX vai identificar o erro “404”. O próprio NGINX já sabe entender isso. Então, quando acontece um erro “404” ou algum daqueles outros que nós colocamos, ele vai reagir como se fosse refazer a requisição para ‘/erro.html’ – e quando acessamos ‘/erro.html’ é exatamente essa a resposta que nós temos.

[03:01] Se quisermos testar, podemos acessar no navegador ‘localhost/erro.html’ - que é exatamente isso.

[03:06] Então, dessa forma, conseguimos configurar mensagem de erro. Dessa forma temos, bastante simples, um servidor web rodando. Eu posso facilmente agora criar todo meu site com HTML, CSS, imagens e arquivos em geral.

[03:22] Só que nem só para isso serve o NGINX, não é só para ouvir requisições HTTP e te devolver arquivos. Então vamos começar a entender algumas outras funcionalidades que essa ferramenta tão poderosa nos traz, a partir dos próximos capítulos.

@@07
Valores válidos

Aprendemos a configurar páginas de erro neste vídeo através da diretiva error_page, informando códigos e um caminho.
Qual das alternativas a seguir é FALSA sobre configurações de páginas de erro no nginx?

Alternativa correta
Podemos redirecionar diversos erros HTTP para o mesmo caminho
 
Alternativa correta
Precisamos informar pelo menos um código HTTP para definir a página de erro
 
Alternativa correta
Precisamos definir o caminho de um arquivo existente
 
Alternativa correta! Essa afirmação é falsa pois no caminho informado ao error_page nós podemos ter um valor que corresponde a alguma diretiva location.

@@08
Faça como eu fiz

Chegou a hora de você seguir todos os passos realizados por mim durante esta aula. Caso já tenha feito, excelente. Se ainda não, é importante que você execute o que foi visto nos vídeos para poder continuar com a próxima aula.

Continue com os seus estudos, e se houver dúvidas, não hesite em recorrer ao nosso fórum!

@@09
O que aprendemos?

Nesta aula, aprendemos:
Conhecemos o formato de configuração do nginx
Configuramos nosso primeiro servidor web
Aprendemos a lidar com a CLI do nginx
Vimos como configurar páginas de erro

@03-Proxy reverso

@@01
O que é?

[00:00] Boas-vindas de volta a mais um capítulo desse treinamento, onde estamos brincando com o NGINX: um servidor web muito poderoso e que traz várias funcionalidades além de simplesmente ser um servidor web!
[00:12] Então vamos começar agora a falar sobre algumas dessas outras funcionalidades. A mais comum - e eu me arrisco a dizer uma das mais utilizadas - é o conceito de “proxy reverso”.

[00:23] A primeira vez que eu ouvi esse termo eu fiquei simplesmente acenando e sorrindo, sem entender absolutamente nada do que aquilo queria dizer, mas o conceito de proxy reverso não é complicado.

[00:35] Então eu vou tentar trazer para você a ideia por trás de um proxy reverso, antes de implementarmos ele, para que as coisas fiquem claras antes de sairmos digitando configuração.

[00:45] Então, um proxy de rede funciona, basicamente, como se fosse um túnel, um filtro, um intermediário; vamos chamar assim. Então imagine que você está em uma rede corporativa da sua empresa e algumas coisas são bloqueadas nessa rede, você não consegue acessar sites de notícias, site de conteúdo adulto, rede social etc.

[01:06] Isso pode ser feito através de um proxy na rede. Um proxy, basicamente, é um servidor em que todas as requisições daquela rede, antes de irem para internet, passam por esse proxy.

[01:18] Então, você no seu computador do trabalho faz uma requisição para “google.com”. Ao invés de ir diretamente para o servidor do Google, isso passa pelo servidor de proxy (pelas configurações da rede) e desse servidor de proxy, vai para o Google - ou dependendo das regras, ele trava essa requisição ali.

[01:35] Então isso é um proxy de rede, super simplificando. Um proxy reverso é quando, ao invés dessa tarefa estar no lado do cliente, nós temos algo semelhante no lado do servidor. Então essa imagem ilustra bem o que é um proxy reverso.

[01:49] Nós temos os clientes fazendo as requisições. Então, acessando a internet eles vão fazer uma requisição e ao invés de cair no servidor real, no servidor de aplicação ou servidor web, isso vai cair no proxy reverso, vai cair em um servidor web que está na frente de outros servidores web. Porque esse proxy reverso pode fazer algumas tarefas.

[02:11] Ele pode simplesmente receber todas as requisições e mandar para vários outros servidores, sem regra nenhuma. Mas ele pode realizar algumas tarefas, por exemplo, verificar cache, fazer redirecionamento, balanço de carga etc.

[02:25] Então vamos começar pela forma mais rudimentar, simplesmente redirecionando requisições. Eu recebi uma requisição aqui e eu redireciono para outro servidor. Essa é a ideia por trás de um proxy reverso.

[02:39] Quando nós entendemos esse conceito de primeira, ele não parece ser muito útil. Então de início vamos configurá-lo para não ser muito útil mesmo, vamos configurar ele para ser meio bobo, só para entendermos a configuração; depois vamos ver um caso de uso real para isso.

[02:55] Então, resumindo essa teoria por trás de um proxy reverso, é um servidor web que recebe requisições e manda para outros servidores, simples assim. Algumas regras podem acontecer aqui, caso nenhuma regra aconteça, ele não é tão útil assim. Mas quando começamos a implementar regras, um proxy reverso pode ser muito poderoso.

[03:18] Vamos começar no próximo vídeo a configurar um proxy reverso usando NGINX, ou seja, além de servidor web, um NGINX pode servir de proxy reverso. Falei bastante, vamos para a prática no próximo vídeo!

@@02
Nomenclatura

Entendemos neste vídeo a ideia por trás desse nome que pode ser assustador: Proxy Reverso.
Por que o nome é Proxy REVERSO e não apenas Proxy?

Porque o conceito é o oposto do que um proxy faz
 
Alternativa errada! Um proxy reverso faz exatamente o que um proxy faz, mas na outra ponta da conexão.
Alternativa correta
Porque normalmente um proxy fica no lado do cliente
 
Alternativa correta! O conceito padrão de proxy é algo que fica no lado do cliente interceptando os pacotes de rede. Como nesse caso o proxy está no lado do servidor, chamamos de proxy reverso.
Alternativa correta
Porque podemos encaminhar requisições de forma reversa
 
Alternativa errada! Essa afirmação nem faz muito sentido para ser sincero.

@@03
Configurando

[00:00] Vamos finalmente configurar realmente o proxy reverso.
[00:05] O que eu quero fazer? Eu vou configurar daquela forma bem simples e que não vai ter muita utilidade agora, só para conhecermos a sintaxe, para entendermos a lógica. Depois falaremos de mais utilidades reais.

[00:17] O que eu quero fazer é - se você se lembra, nós temos dois servidores rodando: um que no meu computador está na porta 8080, que é aquele com as configurações padrão do NGINX e com a mensagem padrão; e outro que nós criamos na porta 80, na porta padrão do HTTP.

[00:32] O que eu quero fazer é: sempre que eu acessar esse servidor na porta 8080, eu quero que ele faça o proxy reverso para o outro servidor, da porta 80. Então se eu vier aqui, por exemplo, e acessar no navegador http://localhost:8080/teste.html, eu não quero ver essa mensagem de não encontrado, eu quero ver aquela mensagem do arquivo teste, ou seja, eu quero acessar aquele outro servidor.

[00:55] Então vamos voltar para a raiz. Como que podemos fazer isso? Vamos nos arquivos de configuração. Eu vou acessar o arquivo ‘nginx.conf’, que é onde está aquele nosso servidor padrão.

[01:10] Com isso, eu vou na parte de ‘server’ onde temos o nosso ‘root’, ou seja, aqui no nosso ‘location’ onde temos o nosso ‘root’ e o ‘index’. Eu posso tirar esses dois aqui, eu não vou utilizá-los. Eu não vou acessar diretamente algum arquivo, nem nada do tipo.

[01:24] Então o que eu quero fazer é: acessei o ‘location’, alguma coisa nesse servidor, ou seja, partindo de ‘/qualquer-coisa’. O que eu vou fazer é um proxy pass, ou seja, utilizando o conceito de proxy eu vou passar essa requisição para: proxy_pass http://localhost:80;.

[01:45] Só que, de novo - como ‘:80’ é a porta padrão HTTP eu posso remover o ‘:80’. Então, com isso eu tenho uma configuração de proxy reverso. É bastante simples, tudo o que eu preciso informar é a diretiva ‘proxy_pass’ e o destino para onde eu vou mandar essa requisição.

[02:03] Então, com isso eu vou salvar e sair. Vou verificar se eu não digitei nada errado. Aparentemente está tudo certo. E vou recarregar com o comando nginx. -s reload.

[02:14] Agora o que eu espero é: quando eu atualizar o navegador, eu quero ver o arquivo padrão, o ‘Arquivo index’. Então quando eu atualizo, aparece ‘Arquivo index’.

[02:25] Se eu tento acessar o http://localhost:8080/teste.html, eu estou no ‘Arquivo teste’.

[02:31] Se eu tento acessar alguma coisa que não existe, eu espero ver aquela mensagem de erro que configuramos no outro servidor. Escrito ‘Mensagem de erro’.

[02:38] Então, agora quando eu acesso um servidor, ele está fazendo proxy para um outro servidor, pegando a resposta e nos devolvendo.

[02:46] Então, veja como é simples configurarmos um proxy reverso usando o NGINX. Mas, como eu comentei, ainda não tem muita utilidade no que estamos fazendo aqui. Então vamos fazer um cenário um pouco mais útil.

[02:58] Vamos trabalhar com outro servidor fora do NGINX e quando chegar uma requisição para o NGINX, se ele atender à algumas regras, mandamos para esse outro servidor - um servidor de aplicação, por exemplo. Mas vamos fazer isso no próximo vídeo!

@@04
Servidor 2 em 1

[00:00] Agora vamos ver uma utilidade, algo um pouco mais real para nós utilizarmos o proxy reverso do NGINX.
[00:08] Imagine o seguinte cenário: eu tenho um servidor de aplicação rodando alguma coisa e eu tenho também, além de uma aplicação, arquivos no meu sistema. Então, por exemplo: o site da Alura.

[00:21] Eu tenho algum servidor rodando em alguma linguagem, que vai fazer a lógica para verificar quais cursos estão cadastrados no banco de dados, as novidades que foram inseridas, promoções etc.

[00:32] Mas além dessa lógica, eu também tenho arquivos estáticos. Arquivos de CSS para estilizar a página, imagens, o próprio HTML... Enfim. Dessa forma, o que eu tenho?

[00:44] Eu tenho arquivos estáticos que não precisam de processamento nenhum, eu só recebo essa requisição e posso devolver esses arquivos muito rápido; e tenho algumas coisas, algumas URLs, alguns locations que precisam ser processados por um servidor de aplicação.

[01:00] Então, podemos usar o proxy reverso para recebermos requisição e devolvermos muito rápido se for algum arquivo estático - inclusive fazer cache e esse tipo de coisas. Agora, se for alguma rota, alguma URL que precise de lógica e que precisa ser processada, eu mando para o nosso servidor de aplicação.

[01:19] Então o que eu quero fazer é, por exemplo: eu tenho aqui o meu localhost, tenho configurado esse servidor. Eu quero que quando eu tentar acessar alguma coisa .php, ele mande para um servidor que está rodando PHP.

[01:33] Agora se for qualquer coisa que não termine com .php, nós mantemos a nossa regra que já tem lá. Então vamos ver como que podemos fazer isso.

[01:40] Eu vou voltar para o terminal. Vou configurar aquele nosso site que está dentro de ‘servers’, que no meu computador é aquele que temos na porta padrão ‘:80’. Então vou configurar ele aqui que está ouvindo a porta ‘80’ e eu vou adicionar um novo ‘location’. Aqui vamos aprender uma nova regra interessante.

[02:02] Aqui em location, como eu comentei, além de ter strings chapadas eu posso ter expressões regulares. Então vou ter aqui, através do acento til (~), eu indico que eu vou ter uma expressão regular e que ele leve em consideração letras maiúsculas e minúsculas. Se eu quiser ignorar letra maiúscula e minúscula, eu posso usar til e asterisco (~*).

[02:24] Então, o que eu quero fazer é pegar a location de qualquer coisa que termine com .php, location ~ \.php Então eu preciso colocar essa barra invertida aqui porque o ponto (.), em uma expressão regular, significa qualquer carácter. Então eu boto essa barra invertida () para indicar que não é qualquer caracter, é o caractere ponto mesmo.

[02:42] Eu posso indicar ainda que isso deve ser o final desse ‘location’, adicionando o cifrão ($). Dessa forma eu tenho um novo ‘location’.

[02:48] Aqui o que eu quero fazer é um proxy reverso. Eu quero que quando alguma requisição chegar para algo que termine com ‘.php’, eu vou mandar para o servidor de PHP e ele vai processar isso aqui.

[03:00] Então eu posso usar o comando proxy_pass http://localhost:8000. Dessa forma, eu tenho dois ‘locations’. Se é alguma imagem, se é algum arquivo estático, o NGINX já vai devolver diretamente, ou seja, isso vai ser muito rápido. Agora se for alguma algum arquivo PHP, o que ele vai fazer é acessar o servidor de PHP que vai fazer o processamento que tem que fazer.

[03:30] Se você não conhece PHP, você pode usar qualquer outra regra aqui. Você pode usar, por exemplo, um Ngrok para servir; você pode usar o HTTP Server do Node; você pode subir um Tomcat para o Java - mas aqui, para manter muito simples eu vou utilizar o PHP.

[03:46] Não precisa se preocupar em reproduzir exatamente isso. Se você tiver o PHP na sua máquina, faça junto comigo; senão você pode adaptar isso para a sua linguagem.

[03:54] Mas continuando, que eu já estou falando bastante! Vou salvar isso aqui e fazer o reload da configuração. Só que você deve estar se perguntando: “Mas e o servidor que está lá na porta 8000 de PHP?”

[04:06] Então vou subir ele aqui mesmo, sem ter arquivo nenhum, eu vou subir em php -S localhost:8000. Então, teoricamente, sempre que eu fizer uma requisição “/algumacoisa.php’ ele vai mandar para esse servidor aqui de PHP. Como eu não tenho nenhum arquivo PHP, ele vai mostrar que não foi possível encontrar o arquivo.

[04:25] Então, vamos lá! Eu vou tentar acessar no navegador http://localhost/algum.php. E quando eu acesso, ele bate no servidor HTTP.

[04:34] Repare que a resposta é diferente, e se eu vejo aqui no terminal ele acessou realmente o nosso servidor PHP.

[04:40] Então essa é uma aplicação pouco mais real de um proxy reverso. Se nós temos arquivos estáticos, simplesmente devolve. Não precisamos passar por um servidor de aplicação para isso.

[04:50] Agora, se é algo que demanda processamento, nesse caso sim, nós vamos fazer o proxy reverso e mandar para um servidor de aplicação.

[04:58] Quero deixar bem claro que essa não é a única forma de mandarmos para um servidor de aplicação, eu estou mandando diretamente de uma outra requisição HTTP. Só que existem outros protocolos que nós poderíamos utilizar, mas isso pode ser assunto para o futuro.

[05:12] Aqui já vimos na prática como termos uma utilização real de um proxy reverso, mas essa não é nem a única forma de implementarmos e nem a única utilidade. Então no próximo capítulo vamos falar de um cenário bastante real, onde utilizamos proxy reverso.

@@05
Necessidade real

Neste vídeo nós fizemos a aplicação mais comum de um proxy reverso. Fazer com que algumas requisições sejam enviadas para um servidor de aplicação.
Por que realizar um proxy reverso e não apenas deixar o servidor de aplicação lidar com todas as requisições?

Com nginx na frente podemos responder arquivos estáticos muito mais rapidamente.
 
Alternativa correta! Esse é um dos principais motivos. O nginx é um servidor incrivelmente performático, então nós ganhamos muito ao não enviar todas as requisições para o servidor de aplicação. O nginx pode enviar diretamente os arquivos estáticos sem processar nada, além de poder definir cache, compressão, etc.
Alternativa correta
Com nginx na frente nosso servidor de aplicação pode ficar offline às vezes sem problemas.
 
Alternativa correta
Não há nenhum motivo real para realizar esta tarefa.

@@06
Faça como eu fiz

Chegou a hora de você seguir todos os passos realizados por mim durante esta aula. Caso já tenha feito, excelente. Se ainda não, é importante que você execute o que foi visto nos vídeos para poder continuar com a próxima aula.

Continue com os seus estudos, e se houver dúvidas, não hesite em recorrer ao nosso fórum!

@@07
O que aprendemos?

Nesta aula, aprendemos:
Conhecemos o conceito de proxy reverso
Configuramos um proxy reverso na prática
Vimos quando se faz necessário um proxy reverso

#### 23/08/2023

@04-API Gateway

@@01
Múltiplos serviços

[00:00] E aí, pessoal? Boas-vindas de volta a mais um capítulo desse treinamento, onde estamos conhecendo bastante sobre a ferramenta NGINX, que é um servidor web que também serve como proxy reverso e algumas outras coisas ainda vamos ver nesse treinamento.
[00:13] No capítulo anterior falamos bastante sobre proxy reverso, nós aprendemos a configurar e vimos um caso de uso real. Agora vamos falar um pouco de um cenário diferente.

[00:24] Hoje em dia, principalmente, é muito falado sobre o conceito de arquitetura baseada em microsserviços. Então, só quero explicar super resumidamente o que é essa tal de arquitetura de microsserviços - até porque existem treinamentos específicos sobre esse assunto aqui na Alura. Então eu só vou pincelar para eu te mostrar o propósito desse capítulo e, o que vamos alcançar com esse capítulo.

[00:48] Uma aplicação padrão (o que nós chamamos de aplicação monolítica) é uma única aplicação onde a partir do navegador, por exemplo, a partir de um site nós acessamos toda a regra de negócios, que tudo acontece, que faz o acesso ao banco de dados, por exemplo, e que nós recebemos essas informações.

[01:09] Então imagine que o site da Alura é uma aplicação monolítica. Você digita “alura.com.br", essa interface vai chegar lá e vai bater na lógica de negócios. Aí essa lógica de negócios vai acessar o banco de dados para descobrir quais são os cursos cadastrados, ele vai acessar as promoções, ele vai acessar as notícias e os últimos cursos lançados. Isso tudo é processado e entregue para você.

[01:32] Então essa é a ideia por trás de uma aplicação comum, uma aplicação monolítica. Só que existe uma outra forma de modelar, que é utilizando a arquitetura de microsserviços.

[01:43] Então, imagine a parte logada da Alura, “cursos.alura.com.br”. Para você acessar essa parte você precisa ter uma assinatura válida e isso envolve a matrícula acadêmica, envolve a parte financeira, a parte de gamificação, onde você tem um jogador e tem pontos. Então tem muitos detalhes que não necessariamente precisam andar juntos.

[02:06] O que é muito comum nos dias de hoje é você ter uma aplicação que cuida da parte financeira, você ter uma outra aplicação que cuida da parte acadêmica, ter outra aplicação que cuida da parte de gamificação ou jogos e você ter outra aplicação que cuida da parte de streaming de vídeos.

[02:24] Então, dessa forma nós organizamos um sistema em várias pequenas aplicações ou serviços. Então daí vem o nome microsserviços.

[02:35] Só que existe um problema se pensarmos nessa abordagem somente como eu te expliquei. Imagine “cursos.alura.com.br” e imagine que o front-end (a página em si) precisa acessar os dados dos cursos que você está matriculado, precisa exibir para você a sua sua matrícula financeira etc., ou quando sua assinatura foi feita.

[02:58] Então, o front-end vai precisar saber qual é a API, qual é o servidor, qual é o serviço que lida com os detalhes financeiros; qual é o servidor, qual é o serviço, qual é o computador que lida com a parte acadêmica - e às vezes isso pode acabar mudando. Eu posso trocar de um servidor para outro, eu posso mover esse serviço.

[03:20] Além disso, às vezes um serviço roda em mais de um servidor, já é outro serviço em um só, então essa parte de roteamento pode ficar bastante difícil. Como saber qual URL acessar, por exemplo?

[03:33] Então, o que vamos fazer nesse capítulo é - usando o conceito de proxy reverso, usando o que aprendemos com proxy pass, o que eu quero fazer? Por exemplo, eu quero acessar a URL http://localhost:8080/ e quando eu digitar http://localhost:8080/servico1 eu quero acessar um servidor, um serviço, uma parte de uma aplicação.

[03:58] Quando eu digitar http://localhost:8080/servico2, mesmo que eu esteja fazendo a requisição para a mesma URL, para o mesmo servidor, eu quero acessar um outro servidor, uma outra aplicação.

[04:09] Então é isso que vamos implementar, é por isso que esse capítulo está depois da partir de proxy reverso. Porque embora o que iremos aplicar tenha um outro nome, diferente de proxy reverso, nós vamos utilizar os conhecimentos de proxy reverso para fazermos isso.

[04:23] Dessa forma, voltando para o exemplo da Alura, é como se pela interface ele só precisasse conhecer “api.alura.com.br”, por exemplo. Baseado no endpoint, baseado na URL desse servidor, esse nosso proxy reverso manda para algum serviço, manda para o serviço financeiro, para o serviço acadêmico, serviço de gamificação etc.

[04:48] Então é isso que vamos implementar nesse treinamento! No próximo vídeo nós vamos criar dois serviços pequenos e rotear entre eles. Fazer com que uma URL mande para um serviço e outra URL mande para outro serviço. Então te espero no próximo vídeo para botarmos a mão na massa!

@@02
Para saber mais: Microsserviços

O propósito deste treinamento não é ensinar o conceito ou práticas da arquitetura orientada a microsserviços, mas caso você queira conhecer este conceito, pode conferir este treinamento: https://cursos.alura.com.br/course/microsservicos-padroes-projeto

https://cursos.alura.com.br/course/microsservicos-padroes-projeto

@@03
Usando proxy_pass

[00:00] Vamos criar dois serviços de exemplo. Eu vou criar dois novos servidores lá no nosso NGINX e vou criar esses dois novos servidores em um arquivo só, para nós agilizarmos o processo.
[00:15] O que eu vou fazer? Eu vou vir aqui no terminal, pegar o nosso arquivo default, e vou copiar ele para cp /usr/local/etc/nginx/servers/default.conf /usr/local/etc/nginx/servers/microsservicos.conf.

[00:33] Então nesse arquivo ‘microsservicos.conf’ eu vou ter esses nossos dois servidores. Então, vamos lá! Digitei vim /usr/local/etc/nginx/servers/microsservicos.conf.

[00:43] Então aqui eu vou ter dois serviços: um eu vou colocar na porta 8001. Deixe-me numerar as linhas para ficar um pouco mais fácil, com o comando set number. Então eu vou copiar todas as linhas, só que eu não vou precisar da parte do PHP, então deixe-me remover isso daqui. Eu vou copiar essas linhas e colar aqui embaixo.

[01:08] Esse outro serviço vai estar ouvindo a porta 8002. Então tenho dois serviços. Eu vou deixar um na pasta root /Users/vinnicius.dias/Dev/nginx/servico1; e outro na pasta root /Users/vinnicius.dias/Dev/nginx/servico2;.

[01:26] Teoricamente está tudo certo. Vou remover essa error page da última linha, porque eu não quero criar isso. Eu só vou criar a index do ‘servico1’ e a index do ‘servico2’, só isso. Então vou salvar.

[01:37] Vou criar dentro de mkdir Dev/nginx/servico1 Dev/nginx/servico2. Agora eu vou adicionar echo “Serviço 1” > Dev/nginx/servico1/index.html. Então o que eu estou fazendo com essa linha de comando?

[02:02] Eu estou simplesmente adicionando o conteúdo “Serviço 1” dentro desse novo arquivo que vai ser criado. Então criei lá e vou botar o serviço dois também, .echo “Serviço 2” > Dev/nginx/servico2/index.html.

[02:13] Então deixe-me garantir que não escrevi nada errado... Aparentemente está tudo certo. Agora vou usar o comando nginx -s reload. Teoricamente eu tenho dois serviços, vamos testá-los.

[02:22] No navegador, acesse http://localhost:8001. Está com problema de caractere por causa de acento e eu não ter definido o charset, problema nenhum. E ao acessar http://localhost:8002 temos o serviço 2. Então eu tenho dois serviços.

[02:35] Agora o que eu quero fazer é fazer que a partir do nosso localhost: 8080 (que é aquele nosso servidor padrão), eu redirecione. Quando eu chamar “serviço 1”, manda para o nosso localhost: 8001; quando chamar “serviço 2” manda para o localhost: 8002. Deixe-me ver aqui, isso aqui já está funcionando porque dentro da pasta “nginx”, eu tenho lá a pasta “servico2”, então ele buscou o arquivo “index.html”.

[03:10] Eu vou remover isso daqui. Deixe-meu abrir o nosso o usr com o comando vim /usr/local/etc/nginx/nginx.conf. Eu não vou ter mais isso daqui #proxy_pass http://localhost;. Eu não vou passar mais aquele nosso proxy para o localhost:80, não vou ter mais aquele proxy.

[03:31] Então quando eu fizer o reload agora, com o comando nginx -s reload, a página http://localhost:8002/servico2 não vai mais funcionar.

[03:39] Agora o que eu quero fazer é mandar o que está em “/servico2” para o localhost: 8002, e o que está em “servico1” para o localhost: 8001. Então vamos fazer essa configuração, que não vai ser nada difícil.

[03:51] Vou voltar para o terminal e usar o comando ‘vim /usr/local/etc/nginx/nginx.conf’. Eu posso até copiar a linha de location se eu quiser. Vou colar e vou colar de novo aqui.

[04:00] Quando eu acessar location /servico1 { o que eu quero fazer é um proxy_pass http://localhost:8001/;. Aqui eu vou adicionar a barra no final, por quê? Você já vai entender.

[04:15] Deixe-me voltar para o último comando location - que será o location /servico2 { - proxy_pass http://localhost:8002/;.

[04:23] E agora deixe-me te explicar o motivo para aqui na segunda linha do primeiro location eu não ter a barra no final e nessa última linha de location eu ter.

[04:31] Quando eu acessar ‘/servico1’, eu quero mandar para localhost: 8001. Só que se eu digitar ‘/servico1/teste’, eu quero mandar para “localhost:8001/teste” e não para “localhost:8001/servico/teste”.

[04:48] Então se eu remover essa barra aqui, ele vai adicionar o que passamos no location aqui no final. Já, se eu coloco a barra, ele vai ignorar o que recebeu no location do servidor original e mandar só a partir de “servico1”.

[05:02] Então, assim nós teoricamente já temos configurado!

[05:11] Vamos fazer o comando nginx -t para garantirmos que está tudo certo. Em seguida, o comando nginx -s reload. Agora, teoricamente, quando eu acessar no navegador http://localhost:8080/servico1, ele vai acessar o “Serviço 1”; e quando eu vier em http://localhost:8080/servico2, ele vai acessar o “Serviço 2”.

[05:32] Deixe-me renomear com o comando mv Dev/nginx/servico2/index.htlm Dev/nginx/servico2/servico.html. Ao acessar no navegador http://localhost:8080/servico2, ele não tem mais a pasta “index”. Então eu vou acessar http://localhost:8080/servico2/servico.html e tudo continuará funcionando.

[05:51] Porque sempre que eu acesso o “/servico2”, o que vem depois vai ser mandado para aquele nosso servidor localhost: 8002. Então seria a mesma coisa que eu fazer isso daqui.

[06:03] O que fizemos agora foi adicionarmos um servidor que nos redireciona para serviços diferentes e que realizam tarefas específicas.

[06:12] Então, eu poderia, por exemplo, aqui na nossa configuração (imagine aquele seu servidor ‘api.alura.com.br’) colocar aqui na localização ‘/financeiro’ e eu mandar para o nosso serviço de financeiro; ‘/academico’. Eu mandaria para o servidor que tem o serviço acadêmico.

[06:32] Então, dessa forma, eu teria um ponto de entrada para acessar vários servidores. Isso tem um nome bastante específico – e é sobre esse nome e essa técnica que eu vou falar no próximo vídeo!

@@04
Propósito

Aprendemos neste vídeo como ter um servidor web que faz o redirecionamento para outros múltiplos servidores baseado na URL recebida.
Antes de assistir o próximo vídeo, por que você acredita que essa prática pode ser útil?

Para centralizar o acesso a múltiplos serviços em um único host
 
Alternativa correta! Dessa forma todas as requisições podem ser feitas para o mesmo servidor, facilitando a vida do cliente, dentre outras vantagens.
Alternativa correta
Para aumentar a performance de cada servidor
 
Alternativa correta
Não há motivo real para aplicar esta técnica

@@05
Entendendo o conceito

[00:00] Vamos recapitular rapidamente o que nós fizemos no último vídeo, porque foi um pouco complexo, apesar de todos os conceitos que já vimos no capítulo anterior.
[00:12] O que nós fizemos foi ter no servidor que no meu computador está ouvindo a porta 8080. Nós temos dois proxy, ou seja, proxy reverso para dois serviços diferentes.

[00:24] Sempre que eu acessar ‘/servico1 algumacoisa’, esse ‘algumacoisa’ vai ser enviado para o serviço que está rodando na porta 8001. Se eu acessar ‘/servico2 algumacoisa’ esse ‘algumacoisa’ vai ser enviado para o ‘localhost:8002’.

[00:41] Então, dessa forma nós construímos um proxy reverso para dois serviços diferentes - e é muito comum que tenhamos serviços diferentes realizando tarefas diferentes. Principalmente quando utilizamos uma arquitetura orientada a microsserviços.

[00:55] Então o que nós fizemos aqui foi criar serviços diferentes e ter um ponto de entrada através desse servidor para esses serviços - e esse ponto de entrada tem um nome.

[01:07] Em uma arquitetura de microsserviços, quando nós temos um ponto único de entrada, chamamos isso de API Gateway ou de portão de uma API. Então, a partir de uma API Gateway é decidido para onde essa requisição vai ser realmente redirecionada.

[01:23] Então, o problema que essa técnica visa resolver é aquela ideia de clientes acessando livremente cada um dos serviços e de que às vezes um serviço ou domínio muda para outro servidor, ou alguma coisa assim do tipo.

[01:36] E eu não precisaria notificar meu cliente, meu cliente não precisaria saber dessa complexidade, mas ele precisaria se atualizar. Enfim, ele tem acesso demais à minha infraestrutura.

[01:48] Para o cliente, para quem está acessando a nossa API e que usa microsserviços, isso deveria ser transparente. Então nós expomos uma única URL, um único servidor, que é essa nossa API Gateway; e a partir desse servidor (“api.alura.com.br”, por exemplo) ele vai fazer todas as requisições, mandando o endpoint correto e cada uma das requisições vai ser redirecionada para o serviço certo.

[02:14] Então o API Gateway fornece um proxy, como temos utilizado o proxy reverso, para as necessidades reais.

[02:22] Uma desvantagem interessante de falar é que esse portão de entrada pode se tornar um ponto único central de falha. E o que isso quer dizer? Se esse meu servidor, que só redireciona, estiver recebendo muitas requisições, nós podemos ter um downtime, podemos ter um atraso, nós podemos ter uma falha ali naquele ponto e derrubarmos toda a aplicação.

[02:47] Então esse é um ponto crítico que devemos monitorar com bastante cuidado - mas vamos falar um pouco sobre performance ainda, então não vamos entrar tanto nesse detalhe.

[02:57] E o comportamento de um API Gateway é importante conhecermos porque ele pode simplesmente redirecionar as requisições e pode permitir ou não uma requisição, então através do NGINX eu poderia configurar para só deixar as requisições vindas de um IP, por exemplo. Isso seria possível.

[03:16] Eu posso utilizar algum tipo de decorator para adicionar informações necessárias no request ou até para remover da resposta. Eu posso adicionar informações de cache nesse meu API Gateway e eu posso adicionar informações de compressão para melhorar performance. Então eu posso adicionar cabeçalhos na ida e eu posso adicionar cabeçalhos na resposta.

[03:38] Então eu posso modificar esses detalhes e eu também posso acabar limitando o acesso. Assim como eu falei de autorizar ou não, eu posso utilizar essa ideia de API Gateway para limitar o acesso, o conteúdo trafegado e impedir que determinadas URLs sejam acessadas por completo. Então eu posso ter um controle a mais.

[03:59] Claro que, para determinados comportamentos e determinadas configurações, não adianta eu ter somente um proxy reverso. Eu precisaria ter uma aplicação ali no meu API Gateway que redirecionasse as chamadas. Mas para a nossa necessidade, onde eu só preciso redirecionar as chamadas, o NGINX já traz isso pronto, sem precisar de nenhum outro software externo.

[04:20] Então essa é a ideia por trás de um API Gateway usando o NGINX!

[04:24] Eu vou deixar na atividade “Para Saber Mais” uma referência muito interessante com um pouco mais de detalhes, do próprio site do NGINX, mostrando como implementar esse API Gateway.

[04:34] Mas apesar de deixar esse “Para Saber Mais” aqui, eu vou pedir para você ler depois do final desse treinamento. Porque tem mais um conceito que vamos aprender que é utilizado naquele artigo.

[04:44] Então, como eu falei um pouco aqui nessa parte de negrito de ponto central de falha, de receber muitas requisições, eu acho que está na hora de pelo menos começarmos a dar uma introdução ao conceito de performance.

[04:58] O que acontece se um servidor meu de aplicação real que realmente responde as requisições se um servidor meu, não o API Gateway, estiver recebendo muitas requisições a mais do que ele consegue responder? O que nós podemos fazer nesses cenários? Como nós podemos, por exemplo, ter mais de um servidor respondendo a mesma aplicação?

[05:20] Então vamos conversar sobre esses cenários no próximo capítulo!

@@06
Para saber mais: API Gateway
PRÓXIMA ATIVIDADE

Agora que conhecemos o conceito de um API Gateway, temos uma base melhor para esse artigo fenomenal do próprio Nginx:
Deploying NGINX as an API Gateway, Part 1

Vale a pena a leitura.

https://www.nginx.com/blog/deploying-nginx-plus-as-an-api-gateway-part-1/

@@07
Faça como eu fiz
PRÓXIMA ATIVIDADE

Chegou a hora de você seguir todos os passos realizados por mim durante esta aula. Caso já tenha feito, excelente. Se ainda não, é importante que você execute o que foi visto nos vídeos para poder continuar com a próxima aula.

Continue com os seus estudos, e se houver dúvidas, não hesite em recorrer ao nosso fórum!

@@08
O que aprendemos?
PRÓXIMA ATIVIDADE

Nesta aula, aprendemos:
Entendemos o conceito de microsserviços
Usamos proxy reverso para rotear requisições
Aprendemos o conceito de API Gateway
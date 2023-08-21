#### 21/08/2023

CURSO NGINX:
servidor Web, Proxy Reverso e API Gateway

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
PRÓXIMA ATIVIDADE

Aprendemos algumas coisas neste vídeo. O que é um servidor web, como o nginx funciona, etc.
Mas na prática, via de regra, qual o papel de um servidor web?

Ouvir conexões udp em alguma porta configurada
 
Alternativa correta
Ouvir conexões tcp em alguma porta configurada
 
Alternativa correta! Um servidor web vai ficar esperando uma conexão chegar. Quando ela chegar, o servidor web faz seu trabalho, garantindo que a mensagem recebida está no formato HTTP e depois fazendo o que deve fazer segundo suas configurações.
Alternativa correta
Ouvir conexões tcp na porta 80
 
Alternativa errada! Nem só de porta 80 vive a web. A porta pode ser configurada.

@@05
Localhost
PRÓXIMA ATIVIDADE

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
PRÓXIMA ATIVIDADE

Chegou a hora de você seguir todos os passos realizados por mim durante esta aula. Caso já tenha feito, excelente. Se ainda não, é importante que você execute o que foi visto nos vídeos para poder continuar com a próxima aula.

Continue com os seus estudos, e se houver dúvidas, não hesite em recorrer ao nosso fórum!

@@07
O que aprendemos?
PRÓXIMA ATIVIDADE

Nesta aula, aprendemos:
Aprendemos o que é Nginx
Vimos o propósito de um servidor web
Aprendemos a instalar o Nginx
Entendemos como o Nginx funciona
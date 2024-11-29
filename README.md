# Bounty Hacker CTF - TryHackMe

Quando refere-se a portas, umas das coisas que se passa em minha cabeça é o nmap, eu utilizei para verificar os serviços que estão rodando na máquina, quantas portas estão abertas e etc, então para responder a primeira questão utilizaremos o comando:

```
$ nmap -sC -sV -Pn $IP -T4 --min-rate=1000000
```

nmap - ferramenta usada para varredura e auditoria de segurança em redes, permite identificar hosts, serviços e suas versões, sistemas operacionais e outras informações úteis em redes locais ou remotas.

-sC: executa scripts padrão do nmap, para detectar informações de serviços, informações de segurança e outros testes básicos 

-sV: Identifica as versões exatas dos serviços em execução nas portas abertas

-Pn: Geralmente o Nmap tenta determinar se o host alvo está ativo antes de realizar a varredura das portas, fazendo com que pacotes ICMP sejam enviados, esse parâmetro instrui o Nmap a ignorar esse acontecimento e considerar o host ativo, isso é útil em redes com firewalls ou quando o ping é bloqueado 

-T4: Define a velocidade e a agressividade da varredura 

Valores possíveis:
-T0 é muito lento e discreto.
-T3 é equilibrado.
-T5 é extremamente rápido, mas pode ser facilmente detectado.

 –min-rate=1000000: define a taxa mínima de pacotes por segundo, forçando o nmap a mandar uma taxa mínima de 1.000.000 de pacotes por segundo, sendo útil para acelerar a varredura.
 
1- Encontre as portas  abertas na máquina
Resposta: 22/ssh, 21/ftp, 80/http


2- Quem escreveu a lista de tarefas?

É possível observarmos que as portas abertas são 21(FTP), 22(SSH) e 80(HTTP).
Após verificar o site rodando o serviço HTTP, não há nada que nos interesse, então partiremos para o serviço FTP.

O FTP é um serviço responsável pela transferência de arquivos entre sistemas, é possível fazermos o login utilizando o usuário “anonymous” e apenas dar um enter para a senha




Encontramos dois arquivos de texto, obtemos o acesso a eles fora do ftp, utilizando o comando: get locks.txt task.txt, dentro do locks.txt, tem várias palavras confusas, nos indicando ser uma possível wordlist, já no task.txt recebemos a resposta para a segunda pergunta



3- Quem escreveu a lista de tarefas?
Resposta: lin

Observando que, o único serviço que não verificamos ainda é o SSH, precisamos adquirir a senha para o usuário lin, considerando que já temos uma possível wordlist, podemos utilizar o comando Hydra para realizar um ataque de força bruta e adquirir a senha, então o comando estaria:

hydra -L lin -P locks.txt ssh://$IP
4- Qual serviço você pode aplicar força bruta  ao arquivo de texto encontrado?
Resposta: SSH

hydra: é uma ferramenta de ataque de força bruta que realiza diversas tentativas de combinações de senha em serviços de rede para obter credenciais válidas
-L: indica o login ou usuário
-P: indica o arquivo que contém a lista de senhas (wordlist)
ssh://$IP: indica o protocolo com o ip em que o ataque será feito 



5- Qual é a senha do usuário?
Resposta: RedDr4gonSynd1cat3

Agora basta fazermos o login no ssh e inserirmos a senha que encontramos :

ssh lin@10.10.67.136

Estamos dentro, agora para procurar por diretórios e arquivos, digitamos um “ls”, assim encontramos a primeira flag:



user.txt
Resposta: THM{CR1M3_SyNd1C4T3}

Para a última flag, faremos algo chamado de Escalação de Privilégios, é um tipo de ataque no qual o usuário ou o processo eleva seus privilégios para acessar certos recursos, dados ou executar certas ações que estariam restritas, existem dois tipos de escalação sendo:

Escalação vertical: o atacante recebe privilégios mais altos como acessar a conta do root
Escalação horizontal: o atacante ganha acessos a uma conta ou sistema com permissões semelhantes, mas o qual não deveria ter acesso


Então utilizaremos o https://gtfobins.github.io/, que é um repositório de binários e comandos úteis que podem ser usados em uma escalação de privilégios, nesse caso procuraremos por tar e executaremos o comando: 

tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh

tar: é um comando utilizado para compactar e descompactar arquivos, também usado para backup e arquivamento de diretórios
-cf: -c significa criar, então o comando tar está sendo instruído a criar um arquivo de arquivamento, e o f significa “file” (arquivo), indicando o arquivo de destino
/dev/null: é o diretório de origem
–checkpoint=1: instrui o comando tar a executar uma ação em cada etapa do processo, neste caso, ele é configurado para acionar o checkpoint uma vez durante a execução do comando 
--checkpoint-action=exec=/bin/sh: define que, quando o checkpoint for atingido, o comando /bin/shell será executado
/bin/shell: é um shell do sistema que  ao executar /bin/bash , o tar inicia uma nova sessão de shell com os mesmos privilégios do processo tar 

Sabendo disso colocamos um sudo antes pois ao usar sudo, o comando tar será executado com permissões de root, e adicionando o /bin antes do tar, garante que localizemos o tar no diretório /bin, após o comando, colocamos a senha do usuário.




o comando whoami serve para nos mostrar qual é o nosso usuário, podemos ver que conseguimos obter acesso ao root, agora é só ler o arquivo de texto com a flag, digitando o caminho da pasta em que está:



root.txt
Resposta: THM{80UN7Y_h4cK3r}


E chegamos ao fim do CTF! Espero ter ajudado!



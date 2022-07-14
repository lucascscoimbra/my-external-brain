# TCP Handshakes 101: o básico pra iniciar no assunto

Sendo curto, grosso e teórico: **Handshake** é basicamente um processo de coordenação e negociação entre dois dispositivos pra iniciar ou concluir a comunicação entre eles.

Inicialmente temos 2 tipos principais de handshake:


&nbsp;
## Three-Way Handshake


&nbsp;

É o processo designado pela [RFC 793](https://datatracker.ietf.org/doc/html/rfc793#page-30) para estabelecer conexões TCP. 

Pra simplificar: Esse processo é bem similar a um processo que todos nós conhecemos bem, principalmente após o ano de 2020.



&nbsp;
![GIF de uma mulher conversando com um idoso através de uma videoconferência](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/1lnyyio1vj0b9k1sc8mz.gif)



**Videoconferência é iniciada:**

_- Oi, bom dia. Você consegue me ouvir?_
_- Bom dia, consigo te ouvir sim. Meu áudio está ok?_
_- Sim, consigo te ouvir claramente._


&nbsp;

Ou seja, é um processo que confirma se ambos os lados da conversa conseguem se comunicar de forma apropriada.

Dado que uma das características do protocolo TCP é a **confiabilidade**, antes que a comunicação entre os hosts tenha início e a transferência de dados comece, ele utiliza o handshake a fim de confirmar as condições necessárias para estabelecer a conexão. Antes mesmo que qualquer dado seja trocado entre cliente e servidor, deve ser estabelecida uma conexão de confiança entre eles. 

Como o nome indica, um _three-way handshake_  ocorre em 3 etapas, todas envolvendo trocas de pacotes de dados entre cliente e servidor. Ao estabelecer uma conexão, o cliente e o servidor devem sincronizar os números de sequência entre si. Isso ocorre basicamente desta forma:


&nbsp;
1. O cliente toma a iniciativa e inicia a conexão com o servidor. Nesse primeiro contato ele "propõe" a forma de comunicação que vai ser utilizada na "conversa"(em suma: a ordem de envio dos pacotes de dados).
   
2. O servidor responde ao cliente confirmando o recebimento da primeira mensagem e concordando com a ordem de envio dos pacotes.
   
3. O cliente, por sua vez, também confirma que recebeu a mensagem do servidor e inicia a comunicação.




&nbsp;
![Layout simplificado da comunicação entre cliente e servidor via handshake de três vias, ou 3-way handshake](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/po71rhycqxthg0e92px9.png)


&nbsp;
## Flags



&nbsp;
Conexões TCP utilizam bits de controle inseridos no cabeçalho do pacote, nesse caso chamados de flags, para estabelecer os protocolos da transmissão de dados.


![Esquema de um pacote TCP, exibindo os cabeçalhos e as flags que estão no pacote](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/56yc9a0b13ie45zgz1xw.png)


&nbsp;
**ACK:** A principal função dessa flag é confirmar a recepção do pacote no destino.  Como eu havia dito logo ali em cima, uma das características do protocolo TCP é a confiabilidade. O emissor do pacote precisa receber a confirmação de que o pacote chegou ao lugar desejado e que não há a necessidade de retransmitir o mesmo pacote. 

**ISN (Número de sequência inicial) :** Não é uma flag, mas uma sequência única de números que identificam cada pacote. 

**SYN:** Ok, já sabemos como confirmar o recebimento dos pacotes e como identificá-los. Resta agora ordená-los numa sequência que será usada pelos hosts. A flag SYN diz respeito a isso, à sincronização desses números sequenciais dos pacotes. Como os hosts devem concordar na ordem desses números sequenciais, a flag tem essa função de estabelecer a ordem do envio durante a comunicação. A ordem desses números não é padrão ou pré-definida por questões de segurança. Afinal, ao saber a ordem dos números sequenciais, um invasor poderia forjar um desses números.   


&nbsp;
Sabendo disso, podemos entender esse processo com mais detalhes agora:


&nbsp;
1. O cliente envia uma solicitação ao servidor pedindo a sincronização de seus os números de sequência. Um pacote que possui a flag **SYN** ativada é enviado especificando seu número de sequência inicial (**ISN**).
   
2. O servidor envia também sua solicitação ao cliente para sincronização de seus números de sequência (com as flags **SYN** e  **ACK** ativadas), enquanto reconhece e confirma o recebimento da solicitação enviando o número de sequência do cliente incrementado por um (**ISN** + 1).
   
3. O cliente confirma o recebimento enviando um novo pacote com a flag **ACK** e o número de sequência do servidor também incrementado por um (**ISN** + 1).



&nbsp;
![Layout mais detalhado de um handshake de três vias, exibindo como as flags são trocadas entre cliente e servidor](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/8r7imxp9a4u6p05s8w44.png)



&nbsp;
Usando o Wireshark, conseguimos ver o processo acontecendo em sequência:


![Filtro do Wireshark exibindo a troca de pacotes TCP em um 3-way handshake](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/jmuyvlhdmjn2lj1wuss8.png)


&nbsp;
Logo após essa troca de pacotes, o servidor deve receber uma requisição HTTP e a "conversa" é iniciada.




&nbsp;


## Four-Way Handshake





&nbsp;
Ok, já vimos como é o processo de início da conexão dos hosts. O processo de finalização não é tão diferente (inclusive, é descrito pela mesma RFC do handshake de 3 vias, mas numa [página](https://datatracker.ietf.org/doc/html/rfc793#page-37) diferente). Dessa vez, ao invés de 3 segmentos de dados, serão trocados 4 segmentos entre os hosts, justificando o nome _" 4-way handshake"_. 



&nbsp;
Simplificando, o processo acontece da seguinte forma:


1. O cliente não tem mais dados para enviar. Por este motivo, decide encerrar a conexão e envia um pacote, comunicando essa decisão ao servidor.
   
2. Servidor recebe o pacote. Confirma o recebimento deste pacote enviando também um pacote ao cliente.
   
3. O servidor envia um segundo pacote afirmando que não há nada mais dados para enviar e que também encerrará a conexão.
   
4. O cliente responde com um novo pacote, confirmando o recebimento dos pacotes anteriores e encerrando a conexão de fato.



&nbsp;
![Esquema simplificado da comunicação entre cliente e servidor via handshake de quatro vias](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/euvivjwl85152jmzzhdz.png)



&nbsp;
## Flag FIN



&nbsp;
FIN: Nesse tipo de handshake é utlizada a flag FIN. Por ser uma flag de finalização, o nome deriva do termo "Finished" (finalizado/ concluído). Após a conclusão da transferência de dados, cliente e servidor encerram a conexão usando essa flag. 


&nbsp;
Revendo o processo do handshake, agora com as flags:

1. O cliente, ao iniciar o encerramento da conexão, envia um segmento com a flag FIN ativada (bit 1) + o ISN. A flag ACK também está contida no segmento, porém não está ativada (bit 0).
   
2. Ao receber a solicitação de encerramento da conexão, o servidor envia um pacote com a flag ACK ativada, mostrando recebeu a requisição. O "Acknowledgement number" é igual ao ISN recebido acrescido por 1 (ISN + 1). 
   
3. O servidor envia um novo segmento ao cliente, dessa vez com a flag FIN ativada e um novo ISN, para encerrar a conexão. Após esse envio a conexão é encerrada no lado do servidor.
   
4. O cliente recebe a flag FIN do servidor, ativa a flag ACK com o Acknowledgement number igual ao ISN recebido mais 1 (ISN +1) e envia o último pacote. Ao concluir esse passo a conexão é encerrada também no lado do cliente.




&nbsp;
![Layout detalhado de um handshake de quatro vias, exibindo a troca de flags entre cliente e servidor](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/0bwup6x12m04i394bb2x.png)


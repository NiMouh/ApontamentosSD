# Aula 4

## Sockets UDP vs TCP
A comunicação entre dois processos consiste na transmissão de uma mensagem de um **socket num processo** para um **socket noutro processo**.

Nos protocolos internet, as mensagens são enviadas para um **socket**. Um socket é um par de endereços: um endereço IP e um número de porta. Um socket é identificado por um par de endereços IP e número de porta, para que possa começar a receber mensagens.

Para **receber** mensagens, um processo pode usar várias portas simultaneamente, mas não pode partilhar uma porta com outro processo no mesmo computador.

### User Datagram Protocol (UDP)
É um protocolo sem conexão, ou seja, não é necessário estabelecer uma ligação entre o cliente e o servidor antes de enviar mensagens. O cliente envia uma mensagem para o servidor e o servidor responde com uma mensagem. O cliente pode enviar outra mensagem sem esperar pela resposta do servidor. A comunicação é feita por **datagramas**, ou seja, cada mensagem é enviada individualmente e não é necessário esperar pela resposta do servidor.

Operações **não bloqueáveis**:
 - send();

Operações **bloqueáveis**:
 - receive();

Nota: A porta do servidor é conhecida pelos clientes e o cliente pode usar qualquer porta para enviar mensagens para o servidor.

### Transmission Control Protocol (TCP)
É um protocolo orientado a conexão, ou seja, é necessário estabelecer uma ligação entre o cliente e o servidor antes de enviar mensagens. Através dessa ligação, podem comunicar sem terem de indicar o endereço IP ou nº da porta. O cliente envia uma mensagem para o servidor e o servidor responde com uma mensagem. O cliente pode enviar outra mensagem sem esperar pela resposta do servidor. A comunicação é feita por **streams**.

Operações **não bloqueáveis**:
 - connect();
 - send();

Nota: Depois de estabelecida a ligação os processos podem comportar-se como processos pares e não cliente-servidor.

Streams TCP usam:
 - *checksums* para verificar a integridade dos dados;
 - *timeouts* para lidar com a perda de pacotes;
 - *números de sequência* para verificar a ordem dos pacotes e rejeitar pacotes duplicados;

## A serialização de estruturas de dados
A serialização de estruturas de dados em Java é o processo de transformar um objeto Java num fluxo de bytes para que ele possa ser armazenado num arquivo, transmitido via uma rede ou até mesmo armazenado numa base de dados.

Para serializar uma estrutura de dados em Java, o objeto deve implementar a interface **Serializable**. Essa interface não possui nenhum método a ser implementado, ela apenas indica que o objeto pode ser serializado.

Para serializar um objeto em Java, utiliza-se a classe ObjectOutputStream. Essa classe fornece métodos para escrever objetos num fluxo de saída. Para desserializar um objeto, utiliza-se a classe **ObjectInputStream**. Essa classe fornece métodos para ler objetos de um fluxo de entrada.

Existem duas formas de permitir que quaisquer **computadores diferentes** troquem valores:
 - Ter uma **representação externa** comum para os valores: os valores são convertidos para uma representação comum antes de serem enviados e depois de serem recebidos;
 - É enviada **informação sobre o formato dos valores**: os valores são enviados com a informação sobre o seu formato, de forma que o computador de destino possa converter os valores para o seu formato.

Nota: É importante lembrar que a serialização pode ser utilizada para salvar o estado de um objeto num arquivo, por exemplo, mas ela não é adequada para enviar dados sensíveis ou seguros via uma rede, pois o processo de serialização pode ser facilmente intercetado e decodificado por terceiros.

### Middleware
**Middleware** é um ‘software’ que atua como um intermediário entre diferentes aplicações, sistemas ou serviços, permitindo a comunicação e integração entre eles. Ele é responsável por gerir e coordenar a comunicação, transporte, segurança e outras funcionalidades necessárias para permitir que aplicações distribuídos possam interagir de forma transparente.

**Middleware** realize o processo de *marshalling* (serialização) e *unmarshalling* (deserialização).

## Comunicação entre clientes e servidores
É necessário um protocolo que, utilizando um mecanismo de transporte (e.g. TCP ou UDP), permita a conversação entre cliente e servidor

### Protocolo pedido-resposta
O cliente envia um pedido ao servidor e o servidor responde com uma resposta. Neste protocolo é obrigatório ter uma resposta do servidor para cada pedido do cliente.

O procotolo para RMI (Remote Method Invocation) é um protocolo pedido-resposta e é implementado usando três operações base:
 - doOperation - ativa um método remoto;
 - getRequest - espera por um pedido;
 - sendReply - envia uma resposta.

### Semântica perante erros
Caso as três operações sejam implementadas sobre UDP, então sobre do mesmo tipo de erros que ocorrem no protocolo UDP, como:
 - Avarias por omisão;
 - Mensagens perdidas ou não entregues por ordem;

Nota: Como o cliente só pode enviar um pedido de cada vez, pode considerar-se que a recepção de um novo pedido é a **confirmação** da última resposta.
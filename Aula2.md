# Aula 2

## IPC (Inter Process Communication)
É um serviço de comunicação entre processos que permite a troca de mensagens entre processos. O IPC é uma das principais formas de comunicação entre processos.

## Protocolos de comunicação
Ao nível de sistemas operativos, a comunicação entre processos pode ser feita pelos seguintes protocolos:
- **TCP/IP**: Protocolo de comunicação que permite a comunicação entre computadores;
- **UDP**: Protocolo de comunicação que permite a comunicação entre computadores;
- Entre outros;

## Modelos de Programação distribuída
Hardware -> Sistema Operativo -> Middleware -> Aplicações

**Middleware**: É um conjunto de software que permite a comunicação entre aplicações e sistemas operativos. Também fornece uma interface de programação para as aplicações. 

## Modelos IPC 
Os modelos IPC mais importantes são:
- **Memória partilhada**: Permite a comunicação entre processos através de uma região de memória partilhada;
- **Comunicação por mensagens**: Permite a comunicação entre processos através de mensagens;

### Memória partilhada
Neste modelo os processos **partilham** uma região de memória. O acesso a esta região é feito através de uma variável de condição. A sincronização é feita através de semáforos (ou monitores).

### Memória distribuída
Neste caso, cada processo tem a sua **própria memória**. A comunicação entre processos é feita através de mensagens (atráves de um canal de comunicação).

Esta comunicação pode ser:
 - **Síncrona**: O remetente envia uma mensagem e espera pela resposta antes de continuar.
 - **Assíncrona**: O remetente envia uma mensagem e continua sua execução sem esperar pela resposta imediata.

Nota: Comunicação síncrona é um conceito de **baixo nível** (maior efiência). Comunicação assíncrona permite um maior grau de **concorrência**, porém exige que o sistema de execução faça a gestão e o armazenamento das mensagens

## Invocação remota de procedimentos (RPC)
A comunicação entre dois processos diz-se uma **chamada de procedimento remoto** quando, um processo (emissor) envia uma mensagem para outro processo (receptor) e espera (permanece suspenso) pela resposta.

### Variante do RPC
após o envio da mensagem, o emissor prossegue a execução até que precise do resultado (permite maior concorrência), caso o receptor não tenha o resultado, o emissor fica suspenso até que o receptor tenha o resultado. Quando o receptor tem o resultado, ele envia uma mensagem para o emissor com o resultado e espera que o emissor receba a mensagem.

O comando de envio pode nomear directamente o processo receptor:
```md
ENVIA <mensagem> PARA <nome do processo>
```
simetricamente no receptor:
```md
ESPERA <mensagem> DE <nome do processo>
```
Se o receptor apenas estiver interessado em receber determinada
mensagem, não importando quem é o emissor:
```md
ESPERA <mensagem> // o emissor é anónimo o receptor não
```

## Formas de criação de processos
Os processos de um programa distribuído podem ser criados de forma **estática** ou **dinâmica**

### Criação de processos estática
Todos os processos são criados no **início** da execução e a atribuição de recursos é feita **antes** da execução, logo é mais eficiente.

### Criação de processos dinâmica
O sistema ajusta-se às necessidades da aplicação ao longo do tempo, ou seja, os processos são criados **durante** a execução do programa. A atribuição de recursos é feita **durante** a execução, logo é menos eficiente, porém mais flexível.

Nota: A criação estática de processos é apropriada para sistemas dedicados
onde a configuração do sistema é conhecida à partida.

## Modelos de interação por mensagens
Estes são alguns dos modelos de interação por mensagens mais importantes:
- **Comunicação ponto-a-ponto**: Cada processo envia mensagens para um único receptor. O processo que envia a mensagem tem de conhecer o endereço do receptor. A comunicação pode ser síncrona ou assíncrona;
- **Pub/Sub**: O publisher que permite aos outros processos subscreverem um determinado tópico ou evento. O publisher envia mensagens para todos os processos que subscreveram o tópico ou evento. A comunicação é assíncrona. Neste modelo tem 2 tipos de mensagens:
 - **Mensagens de notificação (Push)**: São enviadas quando um evento ocorre;
 - **Mensagens de pedido (Pull)**: São enviadas quando um processo quer obter informação sobre um evento;
- **Request/Reply**: Inclui todos os modelos para os quais para cada mensagem enviada a
um processo existe uma resposta.

## Exemplo prático de um servidor TCP
Exemplo prático de um servidor TCP:
```java
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.net.ServerSocket;
import java.net.Socket;

public class Servidor {
    public static void main(String[] args) {
        try {
            ServerSocket server = new ServerSocket(2000);
            while(true){ // Ciclo que aceita outros clientes
                Socket soc = server.accept();
                ObjectInputStream entrada = new ObjectInputStream(soc.getInputStream());
                ObjectOutputStream saida = new ObjectOutputStream(soc.getOutputStream());
                System.out.println("Ligaçao estabelecida com o cliente");
                while (true){ // Ciclo da conversa com o cliente
                    String msg = (String) entrada.readObject();
                    System.out.println("Mensagem recebida: " + msg);
                    String mensagem = Ler.umaString();
                    saida.writeObject(mensagem);
                    saida.flush();
                    if (mensagem.equals("sair"))
                        break;
                }
                saida.close();
                entrada.close();
                soc.close();
            } // Fim do ciclo da conversa com o cliente
        } catch (Exception e) {
            System.out.println("Erro: " + e.getMessage());
        }
    }
}
```

Exemplo prático de um cliente TCP:
```java
import java.io.ObjectOutputStream;
import java.net.Socket;

public class Cliente {

    public static void main(String[] args) {
        try {
            Socket soc = new Socket("localhost", 2000);
            ObjectOutputStream saida = new ObjectOutputStream(soc.getOutputStream());
            ObjectInputStream entrada = new ObjectInputStream(soc.getInputStream());
            while (true) { // Ciclo da conversa com o servidor
                String mensagem = Ler.umaString();
                saida.writeObject(mensagem);
                saida.flush();
                String msg = (String) entrada.readObject();
                if (msg.equals("sair"))
                    break;
                System.out.println("Mensagem recebida: " + msg);
            }
            saida.close();
            entrada.close();
            soc.close();
        } catch (Exception e) {
            System.out.println("Erro: " + e.getMessage());
        }
    }
}
```


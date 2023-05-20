# Possíveis perguntas (teóricas) para a frequência

## 1. O que é um sistema distribuído?
Um sistema distribuído é um conjunto de computadores ligados
em rede, com software que permite a partilha de recursos.

## 2. Quais são as principais características de um sistema distribuído?
- Comunicação por mensagens (em vez de memória partilhada);
- Concorrência (vários processos a executar em paralelo);
- Partilha de recursos (impressoras, discos, bases de dados, etc.);
- Sistema assíncrono:
  - Não existe um relógio global;
  - Diferentes velocidades de processamento;
  - Não existe limite de tempo para a comunicação;
- Falhas independentes:
  - Um processo pode falhar sem que os outros falhem;
- Hererogeneidade:
  - Diferentes sistemas operativos;
  - Diferentes linguagens de programação;
  - Diferentes tipos de rede;
  - Diferentes tipos de hardware;

## 3. O que entende por computação móvel e ubíqua?
São sistemas distribuídos que integram equipamentos móveis/portáteis e
que permitem a comunicação entre estes e outros equipamentos. Exemplos:
- Smartphones - Computacão móvel;
- Smartwatches - Computação móvel;
- SmartCities - Computação móvel e ubíqua;
- SmartCars - Computação móvel e ubíqua;

## 4. Distinga um sistema de memória partilhada de um sistema de memória distribuída.
Sistema de memória partilhada:
- Os processos acedem a um único espaço de endereçamento;
- Comunicam através de variáveis partilhadas;
- A sincronização é feita através de semáforos, monitores, etc.

Sistema de memória distribuída:
- Os processos acedem a vários espaços de endereçamento disjuntos;
- Comunicam através de mensagens;
- A sincronização e comunicação é integrada.

## 5. O que é um middleware?
Um middleware é um software que permite a comunicação entre
aplicações distribuídas, camada intermédia que permite superar 
o problema da heterogeneidade.

## 6. O que é e para que serve a serialização?
A serialização (marshaling) é o processo de transformar um objeto em bytes, ou
seja, permite transformar um objeto num formato standard para que 
este possa ser transmitido pela rede. Este processo normalmente ocorre
no middleware.

## 7. O que é o protocolo request-reply?
O protocolo request-reply é um protocolo de comunicação entre processos
que consiste no cliente enviar um pedido ao servidor e este responder com
uma resposta. A comunicação é síncrona.

## 8. O que é o protocolo publish-subscribe?
O protocolo publish-subscribe é um protocolo de comunicação entre processos
que consiste no cliente subscrever um tópico e o servidor enviar uma mensagem
para todos os clientes que subscreveram esse tópico. A comunicação é assíncrona.

## 9. Explique a diferença entre a semântica at-most-once e at-least-once.
A semântica at-most-once garante que a mensagem é entregue no máximo uma vez, ou seja
o servidor deteta duplicados. A semântica at-least-once garante que a mensagem é entregue
pelo menos uma vez, mas pode ser entregue mais do que uma vez pois o servidor não deteta duplicados.
A semântica de falhas at-most-once é usada pelo Java RMI. Complementando, a semântica de falhas
maybe, não garante a entrega da mensagem, perante possíveis falhas, os pedidos não são reenviados.

## 10. Compare os tipos de sincronização das três formas de comunicação por mensagens: sockets UDP, sockets TCP e RMI.
- Sockets UDP:
  - Comunicação não garante a entrega por ordem, nem sem duplicados; (maybe)
  - Comunicação assíncrona;
- Sockets TCP:
  - Comunicação garante a entrega por ordem, mas não sem duplicados; (at-least-once)
  - Comunicação síncrona;
- RMI:
  - Comunicação garante a entrega por ordem e sem duplicados; (at-most-once)
  - Usa a semântica de falhas at-most-once;
  - Comunicação síncrona;

## 11. Descreva a comunicação do Java RMI, na forma como são especificados os intervenientes no processo.
- Cliente:
  - Cria um stub/proxy do objeto remoto;
  - Invoca um método no stub;
  - O stub serializa os parâmetros e envia-os para o servidor;
  - O stub fica à espera de uma resposta;
  - Quando recebe a resposta, o stub deserializa-a e devolve-a ao cliente;
  - Termina o processo;
- Servidor:
  - Cria um objeto remoto;
  - Regista o objeto remoto no RMI Registry;
  - Fica à espera de pedidos do cliente;
  - Quando recebe um pedido, executa o método pedido pelo cliente;
  - Devolve o resultado ao cliente;
  - Termina o processo;

## 12. O que é uma factory?
Uma factory é uma classe/objecto que permite instanciar objetos remotos.
Isto é, se um cliente quiser instanciar um objeto remoto, recorre a uma factory.


## 13. Qual a diferença entre um lock de objeto e um lock de classe?
Um lock de objeto é um lock que é associado a um objeto, ou seja,
um objeto só pode ser acedido por um processo de cada vez. Um lock
de classe é um lock geral, ou seja, vários objetos têm o mesmo lock.
Exemplo:
```java
public class A {
  public synchronized void m1() {
    // ...
  }
  public void m2() {
    // ...
  }
    public static synchronized void m3() {
        // ...
    }
}
```
No exemplo acima, o método m1() é um lock de objeto, o método m2() não é um lock,
e o método m3() é um lock de classe. Se tiver 2 objetos, o1 e o2, e 2 threads, t1 e t2,
e se t1 invocar o1.m1() e t2 invocar o2.m1(), as threads não vão bloquear porque os
locks são diferentes. Se t1 invocar o1.m1() e t2 invocar o1.m1(), as threads vão bloquear 
porque os locks são iguais. Se t1 invocar o1.m3() e t2 invocar o2.m3(), as threads vão bloquear
porque os locks são iguais. Existe exclusão mútua sempre que as threads bloqueiam.


## 14. Como funcionam os métodos wait() e notify()?
O método wait() deve ser chamado dentro de um bloco 
*synchronized* e faz com que a thread se suspenda a si própria
até que outra thread invoque o método notify() ou notifyAll()
e esta fique no estado executável outra vez.

## 15. Diga o que entende por arquitetura de um sistema distribuído.
A arquitetura é a estrutura do sistema em termos de localização 
das suas diferentes partes, e representa o papel que cada parte
desempenha e como se inter-relacionam.

## 16. No contexto de Java Enterprise Edition, qual a diferença entre um stateful session bean e um singleton session bean?
Um stateful session bean é um bean que mantém o estado de sessões de clientes, ou seja,
mantém o estado de um cliente durante a sessão. Um singleton session bean é um bean que
é instanciado uma vez e partilhado por todos os clientes, ou seja, é um bean que mantém
o estado de todos os clientes. Adicionalmente, um stateless session bean é um bean que
não mantém o estado de sessão de clientes.

## 17. No contexto de Java Enterprise Edition, o que entende por uma Entity?
Uma entity é uma classe java (POJO) que representa uma tabela numa base de dados relacional.
Cada instância da classe representa uma linha da tabela e cada atributo da classe representa
uma coluna da tabela.

## 18. Compare os algoritmos de sincronização de relógios Cristian (sincronização externa) e Berkeley (sincronização interna).
- Cristian:
    - O cliente pede a hora ao servidor;
    - O servidor responde com a sua hora;
    - O cliente calcula a diferença entre a sua hora e a hora do servidor;
    - O cliente ajusta a sua hora;
- Berkeley:
    - O servidor pede a hora aos clientes;
    - Os clientes respondem com a sua hora;
    - O servidor calcula a média das horas dos clientes;
    - O servidor envia a diferença entre a sua hora e a média das horas dos clientes para os clientes;
    - Os clientes ajustam a sua hora;

## 19. Explique como funcionam os relógios lógicos de Lamport para atribuir timestamps a eventos.
Cada processo tem um contador que é incrementado sempre que o processo executa um evento.
Quando um processo envia uma mensagem, o contador é incluído na mensagem. Quando um processo
recebe uma mensagem, o contador é atualizado para o máximo entre o contador atual e o contador
recebido na mensagem mais um. O contador representa o timestamp do evento.
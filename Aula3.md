# Aula 3

## Threads
É uma sequência de execução num processo. Um processo pode ter uma ou várias threads, e cada uma delas representa uma linha de execução independente no processo. Threads compartilham o **mesmo espaço** de **endereçamento** e **recursos** do processo (pai).

A execução deste processo é uma sequência de execuções pertencente ao conjunto das combinações possíveis de execução das threads. Logo, **não é possível** prever qual thread será executada em um dado momento.

Existem duas formas de criar threads em java:
```java
// Estendendo a classe Thread
public class ThreadTest extends Thread {
    public void run() {
        System.out.println("Hello from a thread!");
    }
    public static void main(String args[]) {
        ThreadTest thread = new ThreadTest();
        thread.start();
    }
}
```
```java
// Implementando a interface Runnable
public class RunnableTest implements Runnable {
    public void run() {
        System.out.println("Hello from a thread!");
    }
    public static void main(String args[]) {
        Thread thread = new Thread(new RunnableTest());
        thread.start();
    }
}
```

### Ciclo de vida de uma thread
Durante a execução de uma thread, ela pode estar em um dos seguintes estados:
 1. **New**: A thread foi criada, mas ainda não foi iniciada.
 2. **Start**: A thread foi iniciada, mas ainda não está em execução.
 3. **Running**: A thread está em execução.
 4. **Yield**: A thread está em execução, mas está temporariamente indisponível para execução.
 5. **Interrupt/Termina o método Run**: A thread está em execução, mas foi interrompida.
 6. **Sleep/Wait**: A thread está em execução, mas está temporariamente indisponível para execução.


### Sincronização de threads
A sincronização de threads é um mecanismo que permite que duas ou mais threads acessem um recurso compartilhado de forma segura. Para isso, é necessário que as threads sejam sincronizadas, ou seja, que uma thread não acesse um recurso enquanto outra thread está acessando-o. Para isso, usados um mecanismo de **lock**.

Nota: O facto de uma thread adquirir o lock associado a um objeto não impede que outra thread adquira o lock associado a outro objeto.

Para o método sincronizado ser um **método de instância**:
 - A Thread adquire o lock associado ao objeto em que o método é invocado;
 - Todas as Threads que tentem executar esse mesmo método terão de esperar que a Thread que o está a executar o termine.

```java
public class Exemplo{
    private int x;
    private static int s;
    public synchronized void M1(){ 
        x++; 
    }
    public static synchronized void M2(){ 
        s++;
    }
} 
```

Para o método sincronizado ser um **método de classe**:
 - A Thread adquire o lock associado à classe em que o método é invocado;
 - Todas as Thread que tentam executar esse mesmo método terão de esperar que o lock seja libertado.

```java
public class Exemplo{
    private int x;
    private static int s;
    public void M1(){
        synchronized (this)
        { x++; }
    }
    public static void M2(){
        try { synchronized (Class.forName("Exemplo") )
        { s++; }
        }
        catch (classNotFoundException e) {
            System.out.println("Error:" + e.getMessage());
        }
    }
}
```

Nota: O método sincronizado se for estático, partilha o lock com todos os objetos da classe. Caso contrário, cada objeto tem o seu próprio lock.

#### Exercício de Exclusão Mútua
Supondo que tenho a seguinte classe:
```java
public class Contador{
    private int valor;
    public Contador(){
        valor = 0;
    }

    public synchronized int m1(){...}

    public static synchronized int m2(){...}

    public int m3(){...}
}
```

Dado dois objetos (Contador c1,c2) e duas threads (Thead t1,t2), quais são os casos de exclusão mútua? Justifique.

|     | t1    | t2    | Justificação                                                                    |
| --- | ----- | ----- | ------------------------------------------------------------------------------- |
| Não | c1.m1 | c2.m1 | Não existe exclusão mútua, pois cada thread tem o seu próprio lock.             |
| Sim | c1.m2 | c2.m2 | Existe exclusão mútua, pois o lock é partilhado por todos os objetos da classe. |
| Não | c1.m3 | c2.m3 | Não existe exclusão mútua, pois cada thread tem o seu próprio lock.             |
| Sim | c1.m1 | c1.m1 | Existe exclusão mútua, pois o lock é partilhado por o próprio objeto            |


## Servidores Multithreading
Um servidor multithreading é um servidor que pode processar **várias** solicitações ao mesmo tempo. Para isso, o servidor cria uma thread para cada **solicitação** que recebe.

### Arquitéturas possíveis
Estas são as arquiteturas possíveis para um servidor multithreading:
 - **Thread-per-request**: Cria uma thread para cada solicitação que recebe.
 - **Thread-per-conection**: Cria uma thread para cada conexão que recebe.
 - **Thread-per-object**: Cria uma thread para cada objeto que recebe.
 - **Thread-pool**: Cria um conjunto de threads que são reutilizadas para processar as solicitações.

Exemplo de um servidor, que comunica a cada cliente que o solicita, a data e a hora do sistema:
```java
// Cliente
import java.io.*;
import java.net.*;

public class Cliente(){
    private Socket socket;
    public Cliente(){
        try{
            socket = new Socket("localhost", 12345);
            ObjectInputStream in = new ObjectInputStream(socket.getInputStream());
            System.out.println(in.readObject());
            socket.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    public static void main(String[] args){
        new Cliente();
    }
}
```
```java
// Servidor
import java.io.*;
import java.net.*;

public class Servidor{
    private ServerSocket serverSocket;
    private Socket socket;
    private Connection connection;
    public Servidor(){
        try{
            ServerSocket serverSocket = new ServerSocket(12345);
        }catch (IOException e){
            e.printStackTrace();
        }

        while(true){
            try{
                socket = serverSocket.accept();
                connection = new Connection(socket);
                connection.start();
            }catch (IOException e){
                e.printStackTrace();
            }
        }
    }

    public static void main(String[] args){
        new Servidor();
    }
}
```
```java
// Connection
import java.io.*;
import java.net.*;

public class Connection extends Thread{
    private Socket socket;
    public Connection(Socket socket){
        super();
        this.socket = socket;
        start();
    }

    public void run(){
        try{
            ObjectOutputStream out = new ObjectOutputStream(socket.getOutputStream());
            out.writeObject("A data e hora do sistema:" + new java.util.Date());
            out.flush();
            socket.close();
        }catch (IOException e){
            e.printStackTrace();
        }
    }
}
```

## Controlo entre Threads
A transferência de controlo entre threads é feita através de **notificações** e **esperas**. Uma thread pode esperar por uma notificação de outra thread, e quando essa notificação é recebida, a thread que a enviou pode continuar a sua execução.

Métodos da classe **Object**:
 - **wait()**: Coloca a thread em estado de espera (suspende-se a si própria).
 - **notify()**: Notifica uma thread que está em estado de espera.
 - **notifyAll()**: Notifica todas as threads que estão em estado de espera.

Nota: Só podem ser executados por uma thread que tenha o lock associado ao objeto (isto é, dentro do bloco **synchronized**).

### Wait (Espera)
O método **wait()** coloca a thread no estado **não executável** até que uma destas condições se verifique:
 - A thread é notificada por outra thread;
 - A thread é interrompida por outra thread;
 - O tempo limite de espera expira (caso adicionado);

Após isso, a thread é removida do estado de espera e coloca-se no estado de **execução** e volta ao ponto imediatamente anterior à chamada do método *wait()*.

### Notify/NotifyAll (Notificação)
O método **notify()** notifica uma thread que está em estado de espera. Caso o *wait set* não esteja vazio, é escolhida **uma thread** para ser removida do *wait set* e colocada no *entry set*. Caso contrário, não acontece nada.

O método **notifyAll()** notifica todas as threads que estão em estado de espera. Caso o *wait set* não esteja vazio, **todas as threads** são removidas do *wait set* e colocadas no *entry set*. Caso contrário, não acontece nada.

Nota: o processo que executa o notify, não é suspenso (se possível, o notify deve ser a última instrução do método)

Exemplo de uma aplicação para **gerir o dinheiro numa caixa de um clube recreativo**.
```java
// Utilizador
public class Utilizador extends Thread {
    private final ContaBancaria conta;
    private final long intervalo;

    public Utilizador(ContaBancaria conta, long intervalo) {
        this.conta = conta;
        this.intervalo = intervalo;
        start();
    }

    public void run() {
        while (true) {
            double valor = Math.random() * 50;
            try {
                conta.levantamento(valor);
                System.out.println("Utilizador " + Thread.currentThread().getId() + " levantou " + valor + " euros");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            try {
                Thread.sleep(intervalo);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```
```java
// ContaBancaria
public class ContaBancaria{
    private double saldo;

    public ContaBancaria(double saldo) {
        this.saldo = saldo;
    }

    public double verSaldo() {
        return saldo;
    }

    public boolean semSaldo() {
        return saldo <= 0;
    }

    public synchronized void deposito(double valor) {
        saldo += valor;
        notifyAll();
    }

    public synchronized void levantamento(double valor) throws InterruptedException {
        while (semSaldo()){
            wait();
        }
        saldo -= valor;
    }

    public String toString() {
        return "Saldo: " + saldo;
    }
}
```
```java
// Main
public class Main {
    public static void main(String[] args) {
        ContaBancaria conta = new ContaBancaria(1000);
        Financiador financiador = new Financiador(conta, 5000);
        Utilizador utilizador1 = new Utilizador(conta, 1000);
        Utilizador utilizador2 = new Utilizador(conta, 1500);
        Utilizador utilizador3 = new Utilizador(conta, 2000);
    }
}
```

Exemplo do **"Readers-Writers Problem"**:
```java
// Livro
public class Livro {
    private int XPTO;
    private int numeroLeitores;

    public Livro(int XPTO) {
        this.XPTO = XPTO;
    }
    
    public synchronized int ler(){
        return XPTO;
    }

    public void comecarLeitura(){
        numeroLeitores++;
    }

    public void terminarLeitura(){
        numeroLeitores--;
        if(numeroLeitores == 0){
            notifyAll();
        }
    }

    public boolean temLeitores(){
        return numeroLeitores > 0;
    }

    public void escrever() throws InterruptedException{
        while(tempLeitores()){
            wait();
        }
        XPTO += 100;
        XPTO -= 100;
        notifyAll();
    }
}
```
```java
// Leitor
public class Leitor extends Thread {
    private Livro livro;
    private static final int TEMPO_ESPERA = 1000;
    public Leitor(Livro livro) {
        super();
        this.livro = livro;
        start();
    }

    public void run(){
        while(true){
            livro.comecarLeitura();
            System.out.println("Leitor " + Thread.currentThread().getId() + " leu " + livro.ler());
            livro.terminarLeitura();
            try {
                Thread.sleep(TEMPO_ESPERA);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```
```java
// Escritor
public class Escritor extends Thread{
    private Livro livro;
    private static final int TEMPO_ESPERA = 1000;
    public Escritor(Livro livro) {
        super();
        this.livro = livro;
        start();
    }

    public void run(){
        while(true){
            try{
                livro.escrever();
                System.out.println("Escritor " + Thread.currentThread().getId() + " escreveu");
            }catch(InterruptedException e){
                e.printStackTrace();
            }
            try {
                Thread.sleep(TEMPO_ESPERA);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```
```java
// Main
public class Main{
    public static void main(String[] args){
        Livro livro = new Livro(0);
        Leitor leitor1 = new Leitor(livro);
        Leitor leitor2 = new Leitor(livro);
        Leitor leitor3 = new Leitor(livro);
        Escritor escritor1 = new Escritor(livro);
        Escritor escritor2 = new Escritor(livro);
    }
}
```

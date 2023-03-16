# Aula 3

## Threads
É um sequência de execução dentro de um processo. Cada thread tem seu próprio conjunto de registradores e espaço na pilha. Threads compartilham o **mesmo espaço** de **endereçamento** e **recursos** do processo (pai).

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
        try { synchronized (Class.forName( “Exemplo”) )
        { s++; }
        }
        catch (classNotFoundException e) {...}
    }
}
```

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




# Aula 5

## O Modelo de Objetos
Na **Programação Orientada a Objetos** (POO), o **modelo de objetos** é um conjunto de classes e objetos que descrevem a estrutura e o comportamento de um sistema. É uma abstração que inclui classes (modelos para objetos), objetos (instâncias de classes) e relações entre objetos (herança, polimorfismo e associações).

Nos **Sistemas Distribuídos** (SD), o modelo de objetos é um pouco diferente do que em sistemas centralizados, uma vez que os objetos e classes estão distribuídos em **diferentes nós** ou **dispositivos de rede**, em vez de estarem todos num único local. Os objetos podem estar localizados em diferentes **locais físicos**, e a comunicação entre esses objetos pode envolver a **transmissão de mensagens** através da rede.

## Métodos locais vs Métodos remotos
Existem duas categorias principais de métodos usados para invocar a funcionalidade num sistema distribuído: 

**Métodos locais**: São aqueles executados dentro do mesmo processo ou máquina em que o programa está a ser executado. Eles são chamados de "locais" porque não envolvem a comunicação com outros processos ou dispositivos de rede.

**Métodos remotos**: São aqueles que são executados num processo ou máquina diferente daquela em que o programa está a ser executado. Eles são chamados de "remotos" porque envolvem a comunicação com outros processos ou dispositivos de rede para solicitar que o método seja executado numa máquina remota.

Nota: Ao chamar um método remoto, é necessário estabelecer uma conexão de rede entre o processo ou dispositivo local e o processo ou dispositivo remoto, enviar uma solicitação de execução do método para o outro lado da conexão, esperar pela execução do método no lado remoto e, finalmente, receber o resultado do método de volta no processo ou dispositivo local.

## Excepções locais vs Excepções remotas
A principal diferença entre exceções locais e remotas é o local onde elas são **lançadas** e **capturadas**:

**Excepções locais**: São aquelas que são lançadas e capturadas no mesmo processo ou máquina em que o programa está a ser executado. Eles são chamados de "locais" porque não envolvem a comunicação com outros processos ou dispositivos de rede.

**Excepções remotas**: São aquelas que são lançadas e capturadas num processo ou máquina diferente daquela em que o programa está a ser executado. Eles são chamados de "remotos" porque envolvem a comunicação com outros processos ou dispositivos de rede para solicitar que o método seja executado numa máquina remota.

## Implementação do RMI

### Dispatcher
É responsável por receber as chamadas de método remotas no lado do servidor e **direcioná-las** para o objeto remoto apropriado.

### Skeleton
É responsável por receber a chamada de método remoto no lado do servidor e **encaminhá-la** para o objeto remoto correspondente.

### Exemplo de implemtenação do RMI

Definição da interface Remota:
```java
import java.rmi.Remote;
import java.rmi.RemoteException;

public interface CalculadoraRemota extends Remote {

    public int somar(int a, int b) throws RemoteException;

    public int subtrair(int a, int b) throws RemoteException;

    public int multiplicar(int a, int b) throws RemoteException;

    public int dividir(int a, int b) throws RemoteException;
}
```
Implementação da interface remota:
```java
import java.rmi.RemoteException;
import java.rmi.server.UnicastRemoteObject;

public class CalculadoraRemotaImpl extends UnicastRemoteObject implements CalculadoraRemota {

    public CalculadoraRemotaImpl() throws RemoteException {
        super();
    }

    public int somar(int a, int b) throws RemoteException {
        return a + b;
    }

    public int subtrair(int a, int b) throws RemoteException {
        return a - b;
    }

    public int multiplicar(int a, int b) throws RemoteException {
        return a * b;
    }

    public int dividir(int a, int b) throws RemoteException {
        return a / b;
    }
}
```
Implementação do servidor:
```java
import java.rmi.Naming;
import java.rmi.registry.LocateRegistry;

public class ServidorCalculadora {

    public static void main(String[] args) {
        try {
            LocateRegistry.createRegistry(1099); // Cria um registo RMI na porta 1099
            Naming.rebind("CalculadoraRemota", new CalculadoraRemotaImpl());  // Associar o nome "CalculadoraRemota" ao objeto remoto
            System.out.println("Servidor pronto!");
        } catch (Exception e) {
            System.out.println("Erro: " + e.getMessage());
        }
    }
}
```
Implementação do cliente:
```java
import java.rmi.Naming;

public class ClienteCalculadora {

    public static void main(String[] args) {
        try {
            CalculadoraRemota c = (CalculadoraRemota) Naming.lookup("rmi://localhost/CalculadoraRemota"); // Obter a referência do objeto remoto
            System.out.println("2 + 2 = " + c.somar(2, 2));
            System.out.println("2 - 2 = " + c.subtrair(2, 2));
            System.out.println("2 * 2 = " + c.multiplicar(2, 2));
            System.out.println("2 / 2 = " + c.dividir(2, 2));
        } catch (Exception e) {
            System.out.println("Erro: " + e.getMessage());
        }
    }
}
```
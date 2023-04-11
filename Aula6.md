# Aula 6

## Modelo Assimétrico (Arquitectura Cliente-Servidor)
O modelo assimétrico é um modelo de arquitectura de sistemas distribuídos que consiste em um **servidor** e um ou mais **clientes**. O servidor é um computador que fornece serviços a outros computadores, chamados de clientes. Os clientes são computadores que solicitam serviços ao servidor. O servidor pode fornecer serviços a vários clientes ao mesmo tempo.

## Camadas de um sistema distribuído

### Camada de Apresentação
Camada responsável pela interacção com o utilizador.

### Camada de Aplicação
Camada responsável pela interface com o utilizador.

### Camada lógica de negócio
Camada responsável pelo controlo da aplicação.

### Camada de persistência de dados
Camada responsável pelo armazenamento de dados.

## Servidor particionado
Um servidor particionado refere-se a um servidor dividido em várias partes independentes, cada uma executando uma instância separada do ‘software’ do servidor. Essas partes independentes são chamadas de partições ou nós de servidor.

Cada partição pode ser executada num ‘hardware’ separado e pode ser gerida por um processo separado. Essa abordagem de particionamento é útil em sistemas distribuídos porque permite que diferentes partes do servidor sejam executadas em diferentes máquinas, aumentando a capacidade de processamento e a escalabilidade do sistema.

## Servidor Replicado
Um servidor replicado refere-se a um servidor que é executado em várias máquinas, cada uma executando uma instância separada do ‘software’ do servidor. Essas instâncias são chamadas de réplicas do servidor.

## Código Móvel
Código móvel refere-se a um programa que pode ser executado em diferentes plataformas de hardware e/ou software. O código móvel é um dos principais requisitos para a computação móvel e ubíqua.

# Exercício 1
Classe 'Doadores'
```java

public class Doadores{
    private ArrayList<String> nomes;
    private double valor;

    public Doadores(){
        this.nomes = new ArrayList<String>();
        this.valor = 0;
    }

    public synchronized void Donativo(String nome, double montante){
        this.valor += montante;
        this.addDoador(nome);
    }

    public synchronized void addDoador(String nome){
        if(!this.nomes.contains(nome)){
            this.nomes.add(nome);
        }
    }

    public synchronized void removeDoador(String nome){
        this.nomes.remove(nome);
    }

    public synchronized void setValor(double valor){
        this.valor = valor;
    }

    public double consultarMontante(){
        return this.valor;
    }

    public ArrayList<String> verDoadores(){
        return this.nomes;
    }
}
```
Classe 'Fundraiser'
```java

public class Fundraiser{
    
    public static void main(String[] args){
        Doadores doadores = new Doadores();
        ServerSocket serverSocket = null;
        Socket socket = null;
        try{
            serverSocket = new ServerSocket(12345);
            while(true){
                socket = serverSocket.accept();
                Connection connection = new Connection(socket, doadores);
            }
        }catch(IOException e){
            e.printStackTrace();
        }
    }
}
```
Classe 'Connection'
```java

public class Connection extends Thread{
    private Socket socket;
    private Doadores doadores;

    public Connection(Socket socket, Doadores doadores){
        super();
        this.socket = socket;
        this.doadores = doadores;
        start();
    }

    public void run(){
        try{
            int opcao = -1;
            ObjectOutputStream saida = new ObjectOutputStream(socket.getOutputStream());
            ObjectInputStream entrada = new ObjectInputStream(socket.getInputStream());
            while (opcao != 4){
                // Opção de Donativo, Opção de Consultar Total, Opção de Ver Doadores, Opção de Sair
                opcao = entrada.readInt();
                switch(opcao){
                    case 1:
                        String nome = (String) entrada.readObject();
                        double montante = (double) entrada.readObject();
                        doadores.Donativo(nome, montante);
                        break;
                    case 2:
                        double valor = doadores.consultarMontante();
                        saida.writeObject(valor);
                        saida.flush();
                        break;
                    case 3:
                        ArrayList<String> doadores = doadores.verDoadores();
                        saida.writeObject(doadores);
                        saida.flush();
                        break;
                    case 4:
                        saida.writeObject("Saindo...");
                        break;
                    default:
                        saida.writeObject("Opção Inválida");
                }
            }
            saida.close();
            entrada.close();
            socket.close();
        }catch(IOException e){
            e.printStackTrace();
        }
    }
}
```
Classe 'Doador'
```java

public class Doador{
    public static void main(String[] args){
        Socket socket = null;
        try{
            socket = new Socket("localhost", 12345);
            ObjectOutputStream saida = new ObjectOutputStream(socket.getOutputStream());
            ObjectInputStream entrada = new ObjectInputStream(socket.getInputStream());
            int opcao = -1;
            while(opcao != 4){
                System.out.println("1 - Donativo");
                System.out.println("2 - Consultar Total");
                System.out.println("3 - Ver Doadores");
                System.out.println("4 - Sair");
                opcao = Integer.parseInt(System.console().readLine());
                saida.writeInt(opcao);
                saida.flush();
                switch(opcao){
                    case 1:
                        System.out.println("Nome: ");
                        String nome = Ler.umaString();
                        saida.writeObject(nome);
                        saida.flush();
                        System.out.println("Montante: ");
                        double montante = Ler.umDouble();
                        saida.writeObject(montante);
                        saida.flush();
                        break;
                    case 2:
                        double valor = (double) entrada.readObject();
                        System.out.println("Valor: " + valor);
                        break;
                    case 3:
                        ArrayList<String> doadores = (ArrayList<String>) entrada.readObject();
                        System.out.println("Doadores: " + doadores);
                        break;
                    case 4:
                        System.out.println("Saindo...");
                        break;
                    default:
                        System.out.println("Opção Inválida");
                }
            }
            saida.close();
            entrada.close();
            socket.close();
        }catch(IOException e){
            e.printStackTrace();
        }
    }
}
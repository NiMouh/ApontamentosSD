# Aula 1

## Sistema distribuído

### Definição
Conjunto de computadores independentes, interligados por uma rede de comunicação, que compartilham recursos e trocam informações.

### Características
Alguns dos principais características de um sistema distribuído são:
 - Comunicação por mensagens;
 - Sistema assíncrono (não é em simultâneo);
 - Concorrência (vários processos podem estar a executar em simultâneo);
 - Partilha de recursos;

### Intranet
Rede de computadores que se comunicam via um modo de comunicação privado, ou seja, não é aberto ao público.

![](Imagens/intranet.png)

*Figura 1 - Intranet*

### Computação Móvel e Ubíqua
- **Computação Móvel**: Computadores que se movem de um lugar para outro:
 - Exemplo: Computadores portáteis, telemóveis, etc.;
- **Computação Ubíqua**: Sistemas cuja utilização está de tal forma integrada na funcionalidade do produto transparente para o utilizador;
- **Sistemas embebidos**: Computadores que estão em outros dispositivos:
 - Exemplo: Computadores em televisões eletrodomésticos, etc.;


### Desafios na implementação de sistemas distribuídos
Alguns dos desafios na implementação de sistemas distribuídos são:
 - **Escabilidade**: Capacidade de um sistema de se adaptar a um aumento de carga de trabalho;
 - **Tolerância a falhas**: Capacidade de um sistema de continuar a funcionar mesmo que ocorra uma falha (mascarando/tolerando a falha);
 - **Segurança**: Garantir que os dados e as aplicações estão protegidos (tanto a sua integridade como a sua confidencialidade);
 - **Transparência**: Capacidade de um sistema de esconder a complexidade da sua implementação;

### Exemplos de transparência na implementação de sistemas distribuídos
Alguns exemplos de transparência são:
 - **Transparência de acesso**: - acesso a recursos locais e a recursos remotos seja feito através das mesmas operações;
 - **Transparência de localização**: - permite que os recursos possam ser acedidos sem o conhecimento da sua localização;
 - **Transparência de concorrência**: - permite que os recursos possam ser acedidos sem o conhecimento da sua concorrência;
 - **Transparência de replicação**: - permite que os recursos possam ser acedidos sem o conhecimento da sua replicação (se o recurso está replicado ou se é o original);
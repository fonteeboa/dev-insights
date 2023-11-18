# **SOLID**

## Intuito do SOLID
Os princípios SOLID são cinco princípios de design de código orientado a objeto que basicamente tem os seguintes objetivos:

- Tornar o código mais entendível, claro e conciso;
- Tornar o código mais flexível e tolerante a mudanças;
- Aumentar a adesão do código aos princípios da orientação a objetos

## O que é SOLID?

SOLID é um acrônimo para cada um dos cinco princípios que fazem parte desse grupo:

### SRP - Princípio da Responsabilidade Única (Single Responsibility Principle)

Uma classe deve ter apenas um motivo para mudar, ou seja, deve ter uma única responsabilidade. Isso significa que uma classe deve realizar apenas uma função ou ação específica.

- Encoraja a coesão, facilitando a manutenção e compreensão do código.
- Reduz acoplamento entre classes.

```
// Exemplo de uma classe com uma única responsabilidade
class Calculadora {
    public int somar(int a, int b) {
        return a + b;
    }
    
    public int subtrair(int a, int b) {
        return a - b;
    }
    
    // Apenas funções relacionadas a cálculos são incluídas nesta classe
}
```

### OCP - Princípio do Aberto/Fechado (Open/Closed Principle)

Entidades de software devem estar abertas para extensão, mas fechadas para modificação. Isso implica que o comportamento de uma classe pode ser estendido sem alterar seu código-fonte.

- Encoraja o uso de abstrações, interfaces e padrões de projeto como Strategy e Factory.
- Ajuda na reutilização do código e na escalabilidade do sistema.

```
// Exemplo usando interface e extensão para adicionar funcionalidades
interface Forma {
    double calcularArea();
}

class Quadrado implements Forma {
    private double lado;
    
    public Quadrado(double lado) {
        this.lado = lado;
    }
    
    public double calcularArea() {
        return lado * lado;
    }
}

class Circulo implements Forma {
    private double raio;
    
    public Circulo(double raio) {
        this.raio = raio;
    }
    
    public double calcularArea() {
        return Math.PI * raio * raio;
    }
}
```

### LSP - Princípio da Substituição de Liskov (Liskov Substitution Principle)

Subtipos devem poder ser substituídos pelos tipos de seus objetos base sem alterar a funcionalidade do programa. Em outras palavras, se S é um subtipo de T, então os objetos do tipo T podem ser substituídos por objetos do tipo S sem afetar a funcionalidade do programa.

- Garante que as subclasses sigam o contrato estabelecido pelas superclasses.
- Ajuda na utilização correta de herança e polimorfismo.

```
// Exemplo de uso de herança seguindo o LSP
class Retangulo {
    protected int largura;
    protected int altura;
    
    public void setAltura(int altura) {
        this.altura = altura;
    }
    
    public void setLargura(int largura) {
        this.largura = largura;
    }
    
    public int getArea() {
        return largura * altura;
    }
}

class Quadrado extends Retangulo {
    public void setAltura(int lado) {
        this.altura = lado;
        this.largura = lado;
    }
    
    public void setLargura(int lado) {
        this.largura = lado;
        this.altura = lado;
    }
}
```

### ISP - Princípio da Segregação de Interface (Interface Segregation Principle)

Muitas interfaces específicas são melhores do que uma interface única geral. Classes não devem ser forçadas a depender de interfaces que não utilizam.

- Evita interfaces "gordas" com muitos métodos não utilizados por todas as classes que a implementam.
- Promove a coesão e a granularidade nas interfaces.

```
// Exemplo de segregação de interfaces
interface TrabalhoRemoto {
    void videoConferencia();
}

interface TrabalhoLocal {
    void escreverDocumento();
    void editarDocumento();
}

class Programador implements TrabalhoRemoto, TrabalhoLocal {
    public void videoConferencia() {
        // Implementação específica para videoconferência
    }
    
    public void escreverDocumento() {
        // Implementação específica para escrita de documento
    }
    
    public void editarDocumento() {
        // Implementação específica para edição de documento
    }
}
```

### DIP - Princípio da Inversão de Dependência (Dependency Inversion Principle)

Módulos de alto nível não devem depender de módulos de baixo nível, ambos devem depender de abstrações. Isso significa que as abstrações não devem depender dos detalhes, mas os detalhes devem depender das abstrações.

- Utiliza inversão de controle e injeção de dependência para promover flexibilidade e extensibilidade.
- Ajuda na escrita de código mais testável e menos acoplado.

```
// Exemplo aplicando injeção de dependência
interface Notificador {
    void notificarUsuario();
}

class NotificadorEmail implements Notificador {
    public void notificarUsuario() {
        // Lógica para notificar via email
    }
}

class Usuario {
    private Notificador notificador;
    
    public Usuario(Notificador notificador) {
        this.notificador = notificador;
    }
    
    public void realizarAcao() {
        // Realizar ação e notificar usuário utilizando o notificador
        notificador.notificarUsuario();
    }
}
```

## Conclusão
Os princípios SOLID oferecem diretrizes valiosas para desenvolvedores, ajudando a criar código mais legível, flexível e de fácil manutenção. Ao compreender e aplicar esses princípios, os desenvolvedores podem criar sistemas mais robustos e escaláveis.
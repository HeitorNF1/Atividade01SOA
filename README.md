# Relatório de Mudanças e Considerações Finais – Sistema de Estacionamento

## Mudanças

### Mudança 0
**Módulo:** `application.properties`  
**Motivo:**  
Foi adicionada a propriedade `spring.datasource.driver-class-name=oracle.jdbc.OracleDriver` e os dados de conexão foram deixados diretamente no `application.properties`. O motivo é que o Spring Boot não estava conseguindo identificar automaticamente o driver do Oracle, resultando no erro "Failed to determine a suitable driver class". Ao informar explicitamente o driver e simplificar para um único arquivo de configuração, garantimos que a aplicação consiga inicializar corretamente a conexão com o banco de dados sem depender de perfis adicionais ou variáveis externas. Isso deixa a configuração mais direta, reduz complexidade e evita falhas na hora de rodar o projeto.

### Mudança 1
**Módulo:** `checkIn`  
**Descrição da mudança:**  
- Adicionadas as validações de:
  - Vaga já ocupada (`existsByVagaAndStatus`)  
  - Capacidade máxima do estacionamento (`countByStatus` vs `maxCapacity`)  

**Motivo:**  
Garantir consistência do estacionamento e evitar conflito de vagas ou excesso de veículos. Antes, o check-in só impedia ticket ABERTO duplicado para o mesmo veículo.

### Mudança 2
**Módulo:** `calcularValor`  
**Descrição da mudança:**  
- Antes: cobrava horas inteiras arredondadas para cima, mínimo 1 hora.  
- Agora:  
  - Implementa diária para períodos iguais ou maiores a 12h (`dailyRate`).  
  - Calcula frações de 30 minutos para cobrança parcial (`halfHours * fractionRate`).  

**Motivo:**  
Tornar o cálculo de valor mais realista e flexível, permitindo tarifas por frações de 30 minutos e diária fixa após 12 horas.

### Mudança 3
**Módulo:** Declaração de variáveis/configurações  
**Descrição da mudança:**  
- Adicionados: `maxCapacity` e `dailyRate` como propriedades configuráveis via `@Value`.  

**Motivo:**  
Permitir ajustes externos via `application.properties` sem alterar código, aumentando flexibilidade e manutenção.

### Mudança 4
**Módulo:** Estrutura geral do código  
**Descrição da mudança:**  
- Código passou de versão compacta/linear para mais legível, com espaçamento e separação de responsabilidades (checagens explícitas, comentários, formatação).  

**Motivo:**  
Melhor manutenção, legibilidade e documentação do comportamento esperado.

### Mudança 5
**Módulo:** `criar` (Veículo)  
**Descrição da mudança:**  
- Antes: apenas validava se a placa já existia (`existsByPlaca`) antes de salvar.  
- Agora: além da validação de placa duplicada, adiciona checagem de capacidade máxima (`totalVeiculos >= limite`) antes de salvar.  

**Motivo:**  
- Evitar que o estacionamento ultrapasse sua capacidade máxima.  
- Aumenta a robustez do método, prevenindo exceções ou inconsistências por excesso de veículos.  
- Mantém a regra de negócio consistente com a validação feita no `checkIn` do `TicketService`.

---

## Considerações Finais

O processo de refatoração e evolução do sistema de estacionamento trouxe diversas melhorias significativas tanto na estrutura do código quanto nas regras de negócio implementadas.

### Maior robustez e consistência do sistema
As validações adicionadas no `checkIn` e no método `criar` do módulo `Veiculo` asseguram que não haja duplicação de veículos ou vagas, e que a capacidade máxima do pátio seja respeitada. Isso evita conflitos e inconsistências nos registros de tickets e veículos.

### Cálculo de tarifas mais preciso e flexível
A implementação da cobrança por frações de 30 minutos e da tarifa diária após 12 horas torna o sistema de cobrança mais realista, permitindo que o estacionamento aplique regras comerciais variadas de forma correta e automatizada.

### Configuração externalizada e simplificada
A inclusão de propriedades configuráveis via `application.properties` para parâmetros como `maxCapacity`, `dailyRate` e `hourlyRate` permite ajustes rápidos sem necessidade de alterar o código-fonte, facilitando a manutenção e a adaptação a novos cenários.

### Melhoria na legibilidade e manutenção do código
A refatoração, com espaçamento, comentários e separação clara de responsabilidades nos métodos, aumenta a clareza e facilita futuras evoluções do sistema, além de reduzir o risco de erros durante a manutenção.

### Alinhamento com os requisitos do projeto
Todas as mudanças atendem aos requisitos principais: reserva de vaga, controle de lotação e regras de preço diferenciadas. Além disso, a base agora está preparada para implementar novas evoluções, como histórico de tickets, notificações ou reservas antecipadas.

### Conclusão
As alterações implementadas não apenas corrigem limitações da versão inicial, mas também tornam o sistema mais confiável, flexível e sustentável, fornecendo uma base sólida para futuras melhorias e integração com novas funcionalidades.

# E-commerce v2 - Projeto de Virtualizacao de APIs

Projeto de virtualizacao de APIs utilizando Postman Mock Server com **fluxo completo encadeado de 6 steps**.

A variavel `email` no Environment controla qual cenario sera executado. O Pre-request Script da Collection faz o mapeamento automatico: cada email corresponde a um conjunto de variaveis de controle que direcionam os mocks para o cenario desejado.

---

## Fluxo de Negocio

```
1 - Login (POST /auth/login)
        |
        v
2 - Catalogo de Produtos (GET /products)
        |
        v
3 - Validar Endereco (POST /checkout/address)
        |
        v
4 - Criacao de Pedido (POST /orders)
        |
        v
5 - Pagamento (POST /payment)
        |
        v
6 - Rastreio do Pedido (GET /orders/tracking)
```

---

## Os 12 Cenarios

| Cenario | Nome | Mock 1 | Mock 2 | Mock 3 | Mock 4 | Mock 5 | Mock 6 | Para em |
|---------|------|--------|--------|--------|--------|--------|--------|---------|
| 1 | Login sem credenciais | 401 | - | - | - | - | - | Mock 1 |
| 2 | Token invalido no catalogo | 200 | 401 | - | - | - | - | Mock 2 |
| 3 | Regiao indisponivel | 200 | 200 | 422 | - | - | - | Mock 3 |
| 4 | Carrinho vazio | 200 | 200 | 200 | 400 | - | - | Mock 4 |
| 5 | Erro no PIX | 200 | 200 | 200 | 201 | 400 | - | Mock 5 |
| 6 | Cartao sem saldo | 200 | 200 | 200 | 201 | 402 | - | Mock 5 |
| 7 | Boleto vencido | 200 | 200 | 200 | 201 | 410 | - | Mock 5 |
| 8 | PIX OK - Em transporte | 200 | 200 | 200 | 201 | 200 | 200 | Completo |
| 9 | PIX OK - Entregue | 200 | 200 | 200 | 201 | 200 | 200 | Completo |
| 10 | Cartao avista OK - Em transporte | 200 | 200 | 200 | 201 | 200 | 200 | Completo |
| 11 | Cartao parcelado OK - Entregue | 200 | 200 | 200 | 201 | 200 | 200 | Completo |
| 12 | Boleto OK - Em transporte | 200 | 200 | 200 | 201 | 200 | 200 | Completo |

---

## Como Trocar o Cenario

Basta alterar a variavel `email` no Environment antes de executar:

1. Abra o **E-commerce v2 Environment**
2. Edite a variavel `email`
3. Digite o email correspondente ao cenario desejado (tabela abaixo)
4. **Save**
5. Execute o Runner

O Pre-request Script da Collection faz o mapeamento automatico de todas as variaveis de controle com base no email informado.

---

## Mapeamento de Email por Cenario

| Email | Cenario | paymentMethod | cardNumber | parcelas | dueDate | deliveryStatus |
|-------|---------|---------------|------------|----------|---------|----------------|
| falha@ecommerce.com | 1 - Login sem credenciais | pix | 4111111111111111 | 1 | 2026-03-10 | transporte |
| token.invalido@ecommerce.com | 2 - Token invalido no catalogo | pix | 4111111111111111 | 1 | 2026-03-10 | transporte |
| cep.invalido@ecommerce.com | 3 - Regiao indisponivel | pix | 4111111111111111 | 1 | 2026-03-10 | transporte |
| carrinho.vazio@ecommerce.com | 4 - Carrinho vazio | pix | 4111111111111111 | 1 | 2026-03-10 | transporte |
| pix.erro@ecommerce.com | 5 - Erro no PIX | pix | 4111111111111111 | 1 | 2026-03-10 | transporte |
| sem.saldo@ecommerce.com | 6 - Cartao sem saldo | credit_card | 0000000000000000 | 1 | 2026-03-10 | transporte |
| boleto.vencido@ecommerce.com | 7 - Boleto vencido | boleto | 4111111111111111 | 1 | 2020-01-01 | transporte |
| pix.transporte@ecommerce.com | 8 - PIX OK - Em transporte | pix | 4111111111111111 | 1 | 2026-03-10 | transporte |
| pix.entregue@ecommerce.com | 9 - PIX OK - Entregue | pix | 4111111111111111 | 1 | 2026-03-10 | entregue |
| cartao.avista@ecommerce.com | 10 - Cartao avista OK | credit_card | 4111111111111111 | 1 | 2026-03-10 | transporte |
| cartao.parcelado@ecommerce.com | 11 - Cartao parcelado OK | credit_card | 4111111111111111 | 3 | 2026-03-10 | entregue |
| boleto.ok@ecommerce.com | 12 - Boleto OK - Em transporte | boleto | 4111111111111111 | 1 | 2026-03-10 | transporte |
| cliente@ecommerce.com | Default - PIX OK - Em transporte | pix | 4111111111111111 | 1 | 2026-03-10 | transporte |

---

## Variaveis Dinamicas

| Variavel | Gerada em | Usada em |
|----------|-----------|----------|
| token | Mock 1 - Login | Mocks 2, 3, 4, 5, 6 |
| userId | Mock 1 - Login | Mock 4 |
| produtoId | Mock 2 - Catalogo | Mock 4 |
| produtoNome | Mock 2 - Catalogo | Mock 4 |
| produtoPreco | Mock 2 - Catalogo | Mock 4 |
| endereco | Mock 3 - Endereco | Mock 4 |
| cidade | Mock 3 - Endereco | Mock 4 |
| estado | Mock 3 - Endereco | Mock 4 |
| orderId | Mock 4 - Pedido | Mocks 5, 6 |
| paymentId | Mock 5 - Pagamento | Mock 6 |
| pixCode | Mock 5 - Pagamento | - |
| boletoCode | Mock 5 - Pagamento | - |
| trackingCode | Mock 6 - Rastreio | - |

---

## Validacoes com Regex

| Campo | Regex | Descricao |
|-------|-------|-----------|
| email | `^[^\s@]+@[^\s@]+\.[^\s@]+$` | Formato valido de email |
| password | `^(?=.*[A-Z])(?=.*\d)(?=.*[@#$%&*!])[A-Za-z\d@#$%&*!]{8,}$` | Min 8 chars, 1 maiuscula, 1 numero, 1 especial |
| token JWT | `^[A-Za-z0-9\-_]+\.[A-Za-z0-9\-_]+\.[A-Za-z0-9\-_]+$` | Formato JWT (3 partes separadas por ponto) |
| CEP | `^\d{5}-\d{3}$` | Formato 00000-000 |
| produtoId | `^PROD-\d{3}$` | Formato PROD-000 |
| orderId | `^ORD-\d{8}-\d{3}$` | Formato ORD-00000000-000 |
| paymentId | `^PAY-\d{8}-\d{3}$` | Formato PAY-00000000-000 |
| trackingCode | `^TRK-[A-Z0-9]{10}$` | Formato TRK-XXXXXXXXXX |

---

## Configuracao do Mock Server

1. Importe a Collection `E-commerce-v2.postman_collection.json` no Postman
2. Importe o Environment `E-commerce-v2.postman_environment.json`
3. Selecione o Environment **E-commerce v2**
4. Acesse **Mock Servers** → **Create Mock Server**
5. Selecione a collection **E-commerce v2 - Cenarios de Pagamento**
6. Copie a URL gerada do Mock Server
7. Cole a URL na variavel `baseUrl` do Environment
8. **Save**

---

## Como Executar

### Passo a passo

1. Configure o Mock Server (veja secao acima)
2. Selecione o Environment **E-commerce v2**
3. Altere o `email` para o cenario desejado (veja tabela de mapeamento)
4. Abra o **Collection Runner**
5. Selecione a collection **E-commerce v2 - Cenarios de Pagamento**
6. Certifique-se que todas as 8 requests estao selecionadas
7. Clique em **Run**

### Observacoes

- O Pre-request Script da Collection configura automaticamente todas as variaveis com base no email
- Nao e necessario alterar ninguna outra variavel manualmente
- Os tests validam tanto cenarios de sucesso quanto de falha
- O fluxo para automaticamente quando um step retorna erro (status != 2xx)

---

## Estrutura da Collection

```
E-commerce v2 - Cenarios de Pagamento/
├── 1 - Login
├── 2 - Catalogo de Produtos
├── 3 - Validar Endereco
├── 4 - Criacao de Pedido
├── 5a - Pagamento PIX
├── 5b - Pagamento Boleto
├── 5c - Pagamento Cartao
└── 6 - Rastreio do Pedido
```

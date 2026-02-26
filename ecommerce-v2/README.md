# E-commerce v2 - Fluxo Completo com 12 Cenarios

Projeto de virtualizacao de APIs utilizando Postman Mock Server com **fluxo completo encadeado**.

A variavel `fluxo` no Environment controla qual cenario sera executado. Cada fluxo percorre todos os 6 mocks em sequencia, e se um step falhar, os proximos sao bloqueados automaticamente com `pm.execution.setNextRequest(null)`.

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

## Os 12 Fluxos

| Fluxo | Nome | Mock 1 | Mock 2 | Mock 3 | Mock 4 | Mock 5 | Mock 6 | Para em |
|-------|------|--------|--------|--------|--------|--------|--------|---------|
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

## Como Trocar o Fluxo

Basta alterar a variavel `fluxo` no Environment antes de executar:

1. Abra o **E-commerce v2 Environment**
2. Edite a variavel `fluxo`
3. Digite o numero do fluxo desejado (1 a 12)
4. **Save**
5. Execute o Runner

---

## Massa de Dados por Fluxo

| Fluxo | email | password | cep | quantidade | paymentMethod | cardNumber | parcelas | dueDate | deliveryStatus |
|-------|-------|----------|-----|------------|---------------|------------|---------|---------|----------------|
| 1 | (vazio) | (vazio) | 01310-100 | 1 | pix | 4111111111111111 | 1 | 2026-03-10 | transporte |
| 2 | cliente@ecommerce.com | Senha@2026 | 01310-100 | 1 | pix | 4111111111111111 | 1 | 2026-03-10 | transporte |
| 3 | cliente@ecommerce.com | Senha@2026 | 99999-999 | 1 | pix | 4111111111111111 | 1 | 2026-03-10 | transporte |
| 4 | cliente@ecommerce.com | Senha@2026 | 01310-100 | 0 | pix | 4111111111111111 | 1 | 2026-03-10 | transporte |
| 5 | cliente@ecommerce.com | Senha@2026 | 01310-100 | 1 | pix_error | 4111111111111111 | 1 | 2026-03-10 | transporte |
| 6 | cliente@ecommerce.com | Senha@2026 | 01310-100 | 1 | credit_card | 0000000000000000 | 1 | 2026-03-10 | transporte |
| 7 | cliente@ecommerce.com | Senha@2026 | 01310-100 | 1 | boleto | 4111111111111111 | 1 | 2020-01-01 | transporte |
| 8 | cliente@ecommerce.com | Senha@2026 | 01310-100 | 1 | pix | 4111111111111111 | 1 | 2026-03-10 | transporte |
| 9 | cliente@ecommerce.com | Senha@2026 | 01310-100 | 1 | pix | 4111111111111111 | 1 | 2026-03-10 | entregue |
| 10 | cliente@ecommerce.com | Senha@2026 | 01310-100 | 1 | credit_card | 4111111111111111 | 1 | 2026-03-10 | transporte |
| 11 | cliente@ecommerce.com | Senha@2026 | 01310-100 | 1 | credit_card | 5555555555554444 | 3 | 2026-03-10 | entregue |
| 12 | cliente@ecommerce.com | Senha@2026 | 01310-100 | 1 | boleto | 4111111111111111 | 1 | 2026-03-10 | transporte |

---

## Variaveis Dinamicas

| Variavel | Gerada em | Usada em |
|----------|-----------|----------|
| token | Mock 1 | Mock 2, 3, 4, 5, 6 |
| userId | Mock 1 | - |
| produtoId | Mock 2 | Mock 4 |
| produtoNome | Mock 2 | Mock 4 |
| produtoPreco | Mock 2 | Mock 4 |
| endereco | Mock 3 | - |
| cidade | Mock 3 | - |
| estado | Mock 3 | - |
| orderId | Mock 4 | Mock 5, 6 |
| orderStatus | Mock 4 | - |
| paymentId | Mock 5 | Mock 6 |
| pixCode | Mock 5 | - |
| boletoCode | Mock 5 | - |
| trackingCode | Mock 6 | - |

---

## Regex Utilizadas

| Campo | Regex | Valido | Invalido |
|-------|-------|--------|---------|
| Email | `^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+
[\.[a-zA-Z]{2,}$` | cliente@ecommerce.com | emailinvalido |
| Senha | `^(?=.*[A-Z])(?=.*[a-z])(?=.*\d)(?=.*[@#$%^&+=!]).{6,}$` | Senha@2026 | 123 |
| Token JWT | `^eyJ[a-zA-Z0-9_-]+\.[a-zA-Z0-9_-]+\.[a-zA-Z0-9_-]+$` | eyJhbG...abc.def | token_invalido |
| CEP | `^\d{5}-\d{3}$` | 01310-100 | 01310100 |
| Produto ID | `^PROD-\d{3}$` | PROD-001 | PRODUTO-INVALIDO |
| Order ID | `^ORD-\d{8}-\d{4}$` | ORD-20260225-0001 | ORDER-INVALIDO |
| Payment ID | `^PAY-\d{8}-\d{4}$` | PAY-20260226-0001 | PAY-123 |
| Cartao | `^\d{16}$` | 4111111111111111 | 4111 |
| Validade | `^(0[1-9]|1[0-2])\/\d{2}$` | 12/28 | 13/28 |
| CVV | `^\d{3,4}$` | 123 | 12 |
| Boleto | `^\d{5}\.\d{5} \d{5}\.\d{6} \d{5}\.\d{6} \d \d{14}$` | 34191.79001 01043.510047... | invalido |
| Tracking | `^[A-Z]{2}\d{9}[A-Z]{2}$` | BR123456789BR | BR-123 |
| Data | `^\d{4}-\d{2}-\d{2}$` | 2026-02-26 | 26/02/2026 |
| Bandeira | `^(VISA|MASTERCARD|ELO|AMEX)$` | VISA | visa |

---

## Como Executar

### 1. Criar Mock Server no Postman
1. Clique com botao direito na collection **E-commerce v2**
2. **Mock Collection**
3. Nome: `E-commerce v2 Mock Server`
4. **Create Mock Server**
5. Copie a URL gerada (ex: `https://xyz.mock.pstmn.io`)

### 2. Importar no Postman
1. Importe primeiro: `E-commerce-v2.postman_environment.json`
2. Depois importe: `E-commerce-v2.postman_collection.json`

### 3. Configurar o Environment
1. Abra o **E-commerce v2 Environment**
2. Preencha `baseUrl` com a URL do Mock Server
3. Configure o `fluxo` com o numero desejado (1 a 12)
4. **Save**

### 4. Executar
1. Clique com botao direito na collection
2. **Run collection**
3. Selecione o **E-commerce v2 Environment**
4. **Run E-commerce v2**

### 5. Trocar o Cenario
1. Edite a variavel `fluxo` no Environment
2. Consulte a tabela **Massa de Dados por Fluxo**
3. Execute novamente

---

## Estrutura da Collection

```
E-commerce v2 - Fluxo Completo (12 Cenarios)
|
|-- Collection Pre-request Script (configura massa por fluxo)
|
|-- 1 - Login
|   |-- Pre-request: valida email/senha (Regex), seta mockStatus
|   |-- Tests: valida JWT, salva token
|   |-- Examples: Sucesso_200, Falha_401
|
|-- 2 - Catalogo de Produtos
|   |-- Pre-request: valida token (Regex), seta mockStatus
|   |-- Tests: valida produtos, salva produtoId
|   |-- Examples: Sucesso_200, Falha_401
|
|-- 3 - Validar Endereco
|   |-- Pre-request: valida CEP (Regex), seta mockStatus
|   |-- Tests: valida endereco, salva cidade/estado
|   |-- Examples: Valido_200, Indisponivel_422
|
|-- 4 - Criacao de Pedido
|   |-- Pre-request: valida produtoId (Regex), seta mockStatus
|   |-- Tests: valida orderId, salva orderId
|   |-- Examples: Criado_201, CarrinhoVazio_400
|
|-- 5 - Pagamento
|   |-- Pre-request: valida cartao/boleto/pix (Regex), seta mockStatus
|   |-- Tests: valida paymentId, salva paymentId
|   |-- Examples: PIX_200, CartaoVista_200, CartaoParcelado_200,
|                 Boleto_200, ErroPIX_400, SemSaldo_402, BoletoVencido_410
|
|-- 6 - Rastreio do Pedido
    |-- Pre-request: valida orderId/paymentId (Regex), seta mockStatus
    |-- Tests: valida tracking code, data entrega
    |-- Examples: EmTransporte_200, Entregue_200
```

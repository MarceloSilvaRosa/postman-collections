# E-commerce v2 - Projeto de Virtualizacao de APIs

## Descricao

Este projeto simula o fluxo completo de um e-commerce utilizando **Mock Server do Postman**. Cada requisicao possui exemplos de sucesso e falha que sao acionados dinamicamente por scripts de pre-requisicao, eliminando a necessidade de um backend real durante o desenvolvimento e testes.

---

## Fluxo de Negocios

```
Login → Catalogo → Endereco → Pedido → Pagamento → Rastreio
  1          2          3         4          5/6/7        8
```

| Passo | Request                  | Metodo | Endpoint                              |
|-------|--------------------------|--------|---------------------------------------|
| 1     | Login                    | POST   | `/auth/login`                         |
| 2     | Catalogo de Produtos     | GET    | `/products`                           |
| 3     | Validar Endereco         | POST   | `/checkout/address`                   |
| 4     | Criacao de Pedido        | POST   | `/orders`                             |
| 5     | Pagamento PIX            | POST   | `/payment/pix`                        |
| 6     | Pagamento Cartao         | POST   | `/payment/card`                       |
| 7     | Pagamento Boleto         | POST   | `/payment/boleto`                     |
| 8     | Rastreio do Pedido       | GET    | `/orders/tracking`                    |

---

## Cenarios de Teste (12 Cenarios)

| # | Mock | Cenario                    | Variavel de Controle         | Status |
|---|------|----------------------------|------------------------------|--------|
| 1 | Login | Login Sucesso              | `loginStatus` = (vazio)      | 200    |
| 2 | Login | Login Falha                | `loginStatus` = `falha`      | 401    |
| 3 | Catalogo | Catalogo Sucesso        | `catalogoStatus` = (vazio)   | 200    |
| 4 | Catalogo | Catalogo Falha (token)  | `catalogoStatus` = `falha`   | 401    |
| 5 | Endereco | Endereco Valido         | `enderecoStatus` = (vazio)   | 200    |
| 6 | Endereco | Regiao Indisponivel     | `cep` = `99999-999`          | 422    |
| 7 | Pedido | Pedido Criado             | `pedidoStatus` = (vazio)     | 201    |
| 8 | Pedido | Carrinho Vazio            | `pedidoStatus` = `falha`     | 400    |
| 9 | Pagamento | PIX Sucesso            | `paymentMethod` = `pix`      | 200    |
|10 | Pagamento | Cartao a Vista         | `parcelas` = `1`             | 200    |
|11 | Pagamento | Cartao Parcelado       | `parcelas` = `3`             | 200    |
|12 | Pagamento | Boleto Sucesso         | `dueDate` = data futura      | 200    |

---

## Variaveis Dinamicas

| Variavel        | Gerada por Mock | Usada por Mock(s)               |
|-----------------|-----------------|----------------------------------|
| `token`         | 1 - Login       | 2, 3, 4, 5, 6, 7, 8             |
| `userId`        | 1 - Login       | (referencia)                    |
| `produtoId`     | 2 - Catalogo    | 4 - Pedido                      |
| `produtoNome`   | 2 - Catalogo    | 4 - Pedido                      |
| `produtoPreco`  | 2 - Catalogo    | 4 - Pedido                      |
| `endereco`      | 3 - Endereco    | (referencia)                    |
| `cidade`        | 3 - Endereco    | (referencia)                    |
| `estado`        | 3 - Endereco    | (referencia)                    |
| `orderId`       | 4 - Pedido      | 5, 6, 7, 8 - Pagamento/Rastreio |
| `paymentId`     | 5/6/7 - Pag.    | 8 - Rastreio                    |
| `pixCode`       | 5 - PIX         | (exibicao)                      |
| `boletoCode`    | 7 - Boleto      | (exibicao)                      |
| `trackingCode`  | 8 - Rastreio    | (referencia)                    |
| `mockStatus`    | Pre-request     | Header `x-mock-response-code`   |
| `cenario`       | Pre-request     | Logica de teste                 |

---

## Validacoes com Regex

| Campo          | Regex                                          | Usado em      |
|----------------|------------------------------------------------|---------------|
| Email          | `^[^\s@]+@[^\s@]+\.[^\s@]+$`                  | 1 - Login     |
| Senha          | `^(?=.*[A-Z])(?=.*\d)(?=.*[@#$%&*!])[A-Za-z\d@#$%&*!]{8,}$` | 1 - Login |
| JWT Token      | `^[A-Za-z0-9-_]+\.[A-Za-z0-9-_]+\.[A-Za-z0-9-_]+$` | 1, 2      |
| Produto ID     | `^PROD-\d{3}$`                                 | 2, 4          |
| CEP            | `^\d{5}-\d{3}$`                                | 3 - Endereco  |
| Order ID       | `^ORD-\d{8}-\d{4}$`                            | 4, 5, 6, 7, 8 |
| Payment ID     | `^PAY-\d{8}-\d{4}$`                            | 5, 6, 7, 8    |
| Cartao         | `^\d{16}$`                                     | 6 - Cartao    |
| Validade       | `^\d{2}\/\d{2}$`                               | 6 - Cartao    |
| CVV            | `^\d{3,4}$`                                    | 6 - Cartao    |
| Bandeira       | `^(VISA\|MASTERCARD\|ELO\|AMEX)$`              | 6 - Cartao    |
| Boleto         | `^\d{5}\.\d{5} \d{5}\.\d{6} \d{5}\.\d{6} \d \d{14}$` | 7 - Boleto |
| Tracking Code  | `^[A-Z]{2}\d{9}[A-Z]{2}$`                     | 8 - Rastreio  |
| Data           | `^\d{4}-\d{2}-\d{2}$`                          | 7, 8          |

---

## Como Executar (Passo a Passo)

### Pre-requisitos

1. Instalar o [Postman](https://www.postman.com/downloads/)
2. Importar a collection: `E-commerce-v2.postman_collection.json`
3. Importar o environment: `E-commerce-v2.postman_environment.json`
4. Selecionar o environment **E-commerce v2** no canto superior direito

### Execucao Manual

1. **Abrir** a collection `E-commerce v2 - Cenarios de Pagamento`
2. **Selecionar** o environment `E-commerce v2`
3. **Preencher** a variavel `baseUrl` com a URL do Mock Server (ver secao abaixo)
4. **Executar** as requests na ordem: 1 → 2 → 3 → 4 → 5 (ou 6 ou 7) → 8
5. Cada request salva automaticamente as variaveis necessarias para a proxima

### Execucao com Collection Runner

1. Clicar no icone `...` ao lado da collection
2. Selecionar **Run collection**
3. Garantir que todas as 8 requests estao selecionadas
4. Clicar em **Run E-commerce v2**

---

## Como Alternar Cenarios de Pagamento

Altere as variaveis de ambiente para controlar qual cenario sera executado:

### PIX (padrao)
```
paymentMethod = pix
```
Execute a request **5 - Pagamento PIX**

### Cartao a Vista
```
paymentMethod = credit_card
parcelas = 1
cardNumber = 4111111111111111
```
Execute a request **6 - Pagamento Cartao**

### Cartao Parcelado
```
paymentMethod = credit_card
parcelas = 3
cardNumber = 4111111111111111
```
Execute a request **6 - Pagamento Cartao**

### Cartao Sem Saldo (falha)
```
paymentMethod = credit_card
cardNumber = 4111111111110000
```
Execute a request **6 - Pagamento Cartao** (retorna 402)

### Boleto
```
paymentMethod = boleto
dueDate = 2026-03-10
```
Execute a request **7 - Pagamento Boleto**

### Boleto Vencido (falha)
```
dueDate = 2026-01-01
```
Execute a request **7 - Pagamento Boleto** (retorna 410)

---

## Configuracao do Mock Server

### Criar o Mock Server no Postman

1. Abrir a collection `E-commerce v2 - Cenarios de Pagamento`
2. Clicar no icone `...` ao lado da collection
3. Selecionar **Mock collection**
4. Preencher:
   - **Mock Server Name**: `E-commerce v2 Mock`
   - **Environment**: `E-commerce v2`
5. Clicar em **Create Mock Server**
6. Copiar a URL gerada (ex: `https://xxxxxxxx.mock.pstmn.io`)

### Configurar a variavel baseUrl

1. Abrir o environment `E-commerce v2`
2. Preencher o campo `baseUrl` com a URL copiada do Mock Server
3. Salvar o environment

### Como funcionam os Mocks

O header `x-mock-response-code` e enviado em todas as requisicoes com o valor da variavel `mockStatus`. O Mock Server do Postman usa este header para selecionar qual exemplo de resposta retornar:

- `mockStatus = 200` → retorna o exemplo de sucesso
- `mockStatus = 201` → retorna o exemplo de criacao
- `mockStatus = 400/401/402/404/410/422` → retorna o exemplo de falha correspondente

Os scripts de pre-requisicao definem o `mockStatus` automaticamente com base nas variaveis de ambiente e nas regras de negocio.

---

## Estrutura do Projeto

```
ecommerce-v2/
├── README.md
├── E-commerce-v2.postman_collection.json
└── E-commerce-v2.postman_environment.json
```

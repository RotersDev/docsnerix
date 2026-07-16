---
title: "LLMs"
description: "Referencia completa da API de Infoprodutos em uma unica pagina para copiar e usar com IAs, agentes e automacoes."
---

Esta pagina consolida a API de Infoprodutos em um unico lugar. Use para copiar o contrato da API e enviar para uma IA, agente, integrador ou ferramenta de automacao.

## Base URL

```txt
https://api.nerix.com.br/api/public/infoproducts/v1
```

## Autenticacao

Todas as rotas exigem API Key de Infoprodutos. A chave sempre inicia com `nerix_builder_`.

Headers aceitos:

```http
Authorization: Bearer nerix_builder_SUA_CHAVE
```

ou:

```http
x-nerix-builder-key: nerix_builder_SUA_CHAVE
```

ou:

```http
x-builder-api-key: nerix_builder_SUA_CHAVE
```

Sem uma API Key valida, a API retorna `401`.

## Limites

Janela: 1 minuto.

| Tipo | Limite |
| --- | ---: |
| `GET` | 300 requisicoes/minuto |
| `POST`, `PATCH`, `DELETE` | 60 requisicoes/minuto |
| `POST /checkouts` | 30 pagamentos Pix/minuto |

Quando a API Key nao e enviada, o limite e aplicado por IP antes da autenticacao.

Resposta de limite excedido:

```json
{
  "error": "Limite de requisicoes excedido para criacao de pagamentos Pix. Aguarde alguns segundos antes de tentar novamente.",
  "retryAfter": 60
}
```

## Validar API Key

```http
GET /me
```

```bash
curl --request GET \
  --url "https://api.nerix.com.br/api/public/infoproducts/v1/me" \
  --header "Authorization: Bearer nerix_builder_SUA_CHAVE"
```

Resposta:

```json
{
  "user_id": 3,
  "scope": "infoproducts",
  "key_id": 4
}
```

## Produtos

### Listar produtos

```http
GET /products
```

Query:

- `search` string opcional: filtra por nome ou descricao.

```bash
curl --request GET \
  --url "https://api.nerix.com.br/api/public/infoproducts/v1/products" \
  --header "Authorization: Bearer nerix_builder_SUA_CHAVE"
```

Resposta:

```json
{
  "data": [
    {
      "id": 4146753,
      "name": "+200 Mapas Mentais para aprender Frances",
      "slug": "200-mapas-mentais-para-aprender-frances",
      "description": "Material digital para estudo.",
      "price": "10.00",
      "is_active": true,
      "is_digital": true,
      "delivery_type": "instant",
      "offers": [
        {
          "id": "577332",
          "name": "Plano Basico",
          "price": 10,
          "promotional_price": null,
          "is_default": true,
          "final_price": 10,
          "checkout_slug": "z6qndhn",
          "checkout_url": "https://pay.nerix.com.br/z6qndhn"
        }
      ],
      "checkout_links": [
        {
          "id": 123,
          "slug": "z6qndhn",
          "url": "https://pay.nerix.com.br/z6qndhn",
          "offer_id": "577332",
          "offer_name": "Plano Basico",
          "offer_price": 10,
          "is_default": true
        }
      ]
    }
  ]
}
```

### Obter produto

```http
GET /products/{productId}
```

```bash
curl --request GET \
  --url "https://api.nerix.com.br/api/public/infoproducts/v1/products/4146753" \
  --header "Authorization: Bearer nerix_builder_SUA_CHAVE"
```

### Criar produto

```http
POST /products
```

Body:

- `name` string obrigatorio.
- `description` string opcional.
- `sales_page_url` string opcional.
- `price` number obrigatorio.

```bash
curl --request POST \
  --url "https://api.nerix.com.br/api/public/infoproducts/v1/products" \
  --header "Authorization: Bearer nerix_builder_SUA_CHAVE" \
  --header "Content-Type: application/json" \
  --data '{
    "name": "Curso de Marketing Digital",
    "description": "Acesso imediato ao curso completo.",
    "sales_page_url": "https://seudominio.com/curso",
    "price": 97
  }'
```

Resposta:

```json
{
  "id": 991122,
  "name": "Curso de Marketing Digital",
  "slug": "curso-de-marketing-digital",
  "price": "97.00",
  "is_active": true,
  "is_digital": true,
  "delivery_type": "instant",
  "offers": [
    {
      "id": "827411",
      "name": "Curso de Marketing Digital",
      "price": 97,
      "promotional_price": null,
      "is_default": true,
      "final_price": 97,
      "checkout_url": "https://pay.nerix.com.br/a1b2c3d"
    }
  ]
}
```

### Atualizar produto

```http
PATCH /products/{productId}
```

Body opcional:

- `name`
- `description`
- `sales_page_url`
- `price`
- `promotional_price`
- `guarantee_days`
- `support_email`
- `producer_display_name`
- `builder_settings`
- `builder_offers`
- `is_active`

```bash
curl --request PATCH \
  --url "https://api.nerix.com.br/api/public/infoproducts/v1/products/991122" \
  --header "Authorization: Bearer nerix_builder_SUA_CHAVE" \
  --header "Content-Type: application/json" \
  --data '{
    "price": 127,
    "guarantee_days": 7,
    "builder_settings": {
      "collect_phone": true,
      "phone_required": true,
      "collect_address": false,
      "require_email_confirmation": true,
      "show_nerix_branding": true
    }
  }'
```

## Ofertas

### Listar ofertas

```http
GET /products/{productId}/offers
```

```bash
curl --request GET \
  --url "https://api.nerix.com.br/api/public/infoproducts/v1/products/4146753/offers" \
  --header "Authorization: Bearer nerix_builder_SUA_CHAVE"
```

Resposta:

```json
{
  "data": [
    {
      "id": "577332",
      "name": "Plano Basico",
      "price": 10,
      "promotional_price": null,
      "is_default": true,
      "final_price": 10,
      "checkout_id": 123,
      "checkout_slug": "z6qndhn",
      "checkout_url": "https://pay.nerix.com.br/z6qndhn"
    }
  ]
}
```

### Criar oferta

```http
POST /products/{productId}/offers
```

Body:

- `name` string obrigatorio.
- `price` number obrigatorio.
- `promotional_price` number ou null opcional.
- `is_default` boolean opcional.

```bash
curl --request POST \
  --url "https://api.nerix.com.br/api/public/infoproducts/v1/products/4146753/offers" \
  --header "Authorization: Bearer nerix_builder_SUA_CHAVE" \
  --header "Content-Type: application/json" \
  --data '{
    "name": "Oferta com Bonus",
    "price": 197,
    "promotional_price": 147,
    "is_default": false
  }'
```

### Obter oferta

```http
GET /offers/{offerId}
```

```bash
curl --request GET \
  --url "https://api.nerix.com.br/api/public/infoproducts/v1/offers/577332" \
  --header "Authorization: Bearer nerix_builder_SUA_CHAVE"
```

### Atualizar oferta

```http
PATCH /offers/{offerId}
```

```bash
curl --request PATCH \
  --url "https://api.nerix.com.br/api/public/infoproducts/v1/offers/577332" \
  --header "Authorization: Bearer nerix_builder_SUA_CHAVE" \
  --header "Content-Type: application/json" \
  --data '{
    "name": "Oferta Especial",
    "price": 197,
    "promotional_price": 127
  }'
```

## Criar pagamento Pix

```http
POST /checkouts
```

Este endpoint cria o pedido e gera a cobranca Pix.

Requisitos:

- A conta precisa ter Carteira Builder cadastrada.
- A Carteira Builder precisa ter provedor Pix ativo.
- O produto precisa existir.
- A oferta precisa existir e ter `checkout_url`.

Body:

- `product_id` number ou string obrigatorio.
- `offer_id` string obrigatorio.
- `quantity` number opcional, padrao `1`.
- `customer.email` string obrigatorio.
- `customer.name` string opcional.
- `customer.document` string opcional.
- `customer.phone` string opcional.
- `coupon_code` string opcional.
- `metadata` object opcional.

```bash
curl --request POST \
  --url "https://api.nerix.com.br/api/public/infoproducts/v1/checkouts" \
  --header "Authorization: Bearer nerix_builder_SUA_CHAVE" \
  --header "Content-Type: application/json" \
  --data '{
    "product_id": 4146753,
    "offer_id": "577332",
    "quantity": 1,
    "customer": {
      "email": "cliente@exemplo.com",
      "name": "Cliente Teste",
      "document": "11144477735",
      "phone": "41999999999"
    },
    "metadata": {
      "utm_source": "api",
      "external_id": "lead_123"
    }
  }'
```

Resposta validada:

```json
{
  "order": {
    "id": 2051569,
    "order_number": "019f68a4-1802-7846-9e50-050d26f81f07",
    "status": "pending",
    "payment_status": "pending",
    "total": "10.00",
    "customer_email": "cliente@exemplo.com",
    "payment_method": "pix"
  },
  "payment": {
    "status": "pending",
    "qr_code": "000201...",
    "qr_code_base64": "data:image/png;base64,...",
    "expiration_date": null
  }
}
```

Use `payment.qr_code` como Pix copia e cola.

Use `payment.qr_code_base64` para exibir imagem pronta do QR Code:

```html
<img src="data:image/png;base64,BASE64_RETORNADO_AQUI" alt="QR Code Pix" />
```

## Pedidos

### Listar pedidos

```http
GET /orders?page=1&limit=20
```

```bash
curl --request GET \
  --url "https://api.nerix.com.br/api/public/infoproducts/v1/orders?page=1&limit=20" \
  --header "Authorization: Bearer nerix_builder_SUA_CHAVE"
```

Resposta:

```json
{
  "data": [
    {
      "id": 123,
      "order_number": "019f3cbc-3db5-7445-b0f0-598baa2af6a0",
      "status": "paid",
      "payment_status": "paid",
      "total": "10.00",
      "customer_email": "cliente@exemplo.com",
      "items": [],
      "payment": {},
      "customer": {}
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 9,
    "total_pages": 1
  }
}
```

### Obter pedido

```http
GET /orders/{orderId}
```

`orderId` pode ser o ID interno ou o `order_number`.

```bash
curl --request GET \
  --url "https://api.nerix.com.br/api/public/infoproducts/v1/orders/019f3cbc-3db5-7445-b0f0-598baa2af6a0" \
  --header "Authorization: Bearer nerix_builder_SUA_CHAVE"
```

### Consultar entrega

```http
GET /orders/{orderId}/delivery
```

```bash
curl --request GET \
  --url "https://api.nerix.com.br/api/public/infoproducts/v1/orders/019f3cbc-3db5-7445-b0f0-598baa2af6a0/delivery" \
  --header "Authorization: Bearer nerix_builder_SUA_CHAVE"
```

Resposta:

```json
{
  "order_id": 123,
  "order_number": "019f3cbc-3db5-7445-b0f0-598baa2af6a0",
  "delivery_status": "pending",
  "sent_at": null,
  "items": [
    {
      "product_id": 4146753,
      "title": "+200 Mapas Mentais para aprender Frances",
      "value": null,
      "delivered_at": null
    }
  ]
}
```

## Clientes

```http
GET /customers
```

```bash
curl --request GET \
  --url "https://api.nerix.com.br/api/public/infoproducts/v1/customers" \
  --header "Authorization: Bearer nerix_builder_SUA_CHAVE"
```

Resposta:

```json
{
  "data": [
    {
      "customer_email": "cliente@exemplo.com",
      "customer_name": "Cliente Teste",
      "customer_phone": "41999999999",
      "total_orders": "2",
      "total_paid": "194.00"
    }
  ]
}
```

## Cupons

### Listar cupons

```http
GET /products/{productId}/coupons
```

### Criar cupom

```http
POST /products/{productId}/coupons
```

Body:

- `code` string obrigatorio.
- `type` string: `percentage` ou `fixed`.
- `value` number obrigatorio.
- `min_purchase` number opcional.
- `max_discount` number opcional.
- `usage_limit` number opcional.
- `is_active` boolean opcional.
- `is_secret` boolean opcional.
- `valid_from` string ISO opcional.
- `valid_until` string ISO ou null opcional.

```bash
curl --request POST \
  --url "https://api.nerix.com.br/api/public/infoproducts/v1/products/4146753/coupons" \
  --header "Authorization: Bearer nerix_builder_SUA_CHAVE" \
  --header "Content-Type: application/json" \
  --data '{
    "code": "BEMVINDO10",
    "type": "percentage",
    "value": 10,
    "usage_limit": 100,
    "is_active": true,
    "valid_until": "2026-12-31T23:59:59-03:00"
  }'
```

### Atualizar cupom

```http
PATCH /products/{productId}/coupons/{couponId}
```

### Remover cupom

```http
DELETE /products/{productId}/coupons/{couponId}
```

## Order bumps

### Listar order bumps

```http
GET /products/{productId}/order-bumps
```

### Criar order bump

```http
POST /products/{productId}/order-bumps
```

Body:

- `offer_product_id` number obrigatorio.
- `builder_offer_id` string opcional.
- `title` string opcional.
- `description` string opcional.
- `cta` string opcional.
- `custom_price` number opcional.
- `show_image` boolean opcional.

```bash
curl --request POST \
  --url "https://api.nerix.com.br/api/public/infoproducts/v1/products/4146753/order-bumps" \
  --header "Authorization: Bearer nerix_builder_SUA_CHAVE" \
  --header "Content-Type: application/json" \
  --data '{
    "offer_product_id": 991122,
    "builder_offer_id": "882173",
    "title": "Adicione o pack premium",
    "description": "Receba tambem os modelos extras.",
    "cta": "Adicionar ao pedido",
    "custom_price": 27,
    "show_image": true
  }'
```

### Reordenar order bumps

```http
POST /products/{productId}/order-bumps/reorder
```

Body:

```json
{
  "bump_ids": [12, 10, 11]
}
```

### Remover order bump

```http
DELETE /products/{productId}/order-bumps/{bumpId}
```

## Erros comuns

### API Key ausente

```json
{
  "error": "API key de Infoprodutos obrigatoria"
}
```

### API Key invalida

```json
{
  "error": "API key de Infoprodutos invalida ou inativa"
}
```

### Carteira Builder incompleta

```json
{
  "error": "Carteira Builder incompleta. Cadastre os dados obrigatorios da carteira antes de criar pagamentos via API."
}
```

### Provedor Pix inativo

```json
{
  "error": "Nenhum provedor Pix ativo na carteira. Ative um provedor Pix nas configuracoes da carteira antes de criar pagamentos."
}
```

### Produto nao encontrado

```json
{
  "error": "Produto nao encontrado"
}
```

### Oferta ou checkout nao encontrado

```json
{
  "error": "Oferta ou checkout nao encontrado"
}
```

## Webhooks

Eventos recomendados para acompanhar vendas:

- `order.created`
- `order.approved`
- `order.cancelled`
- `purchase_refused`
- `checkout_abandoned`
- `chargeback`
- `refund`

Payload exemplo:

```json
{
  "event": "order.approved",
  "created_at": "2026-07-15T21:00:00.000Z",
  "data": {
    "order_number": "019f3cbc-3db5-7445-b0f0-598baa2af6a0",
    "status": "paid",
    "payment_status": "paid",
    "total": "10.00",
    "customer": {
      "email": "cliente@exemplo.com",
      "name": "Cliente Teste"
    },
    "items": [
      {
        "product_id": 4146753,
        "product_name": "+200 Mapas Mentais para aprender Frances",
        "quantity": 1
      }
    ]
  }
}
```

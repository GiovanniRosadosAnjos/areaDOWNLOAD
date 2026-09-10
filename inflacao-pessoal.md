# Inflação Pessoal — Registro de Gastos

**Objetivo:** acompanhar os gastos reais ao longo do ano (notas fiscais por categoria e por item) para calcular, ano a ano, a inflação pessoal aplicada ao meu estilo de vida, em comparação com o índice oficial (IPCA/INPC), e identificar quais produtos específicos estão puxando o aumento (ex.: carne, óleo de soja, frutas, legumes).

Este arquivo é o registro mestre (editado diretamente, com histórico de versões pelo Git). A cópia no Google Drive é sincronizada a partir daqui, quando solicitado.

## Categorias e subcategorias (inicial, ajustável)
- **Alimentação**: Carnes, Grãos/Cereais, Frutas, Verduras/Legumes, Laticínios, Padaria/Doces, Bebidas, Higiene/Limpeza, Outros
- Moradia
- Transporte
- Saúde
- Lazer
- Assinaturas/Serviços digitais
- Outros

## Formato de registro

A partir de 06/09/2026, os gastos são lançados **por item** (produto individual, com preço unitário), não mais como total agregado da nota — isso permite comparar o preço de um mesmo produto ao longo do tempo. Notas anteriores a essa data, sem detalhamento por item disponível, ficam registradas como total agregado.

**Valor unitário vs. valor total:** o valor unitário (R$/kg, R$/un) mede preço; o valor total mede quanto foi gasto (preço × quantidade). Comparações de "inflação"/preço usam sempre o valor unitário — o valor total sozinho não separa "ficou mais caro" de "comprei mais".

**Descontos por item:** quando a nota traz um desconto aplicado a um item específico, o valor unitário e o valor total registrados já refletem o preço efetivamente pago (líquido do desconto), com uma observação na coluna Item indicando o preço de tabela original.

## 2026

### Setembro — Lançamentos itemizados
| Data | Categoria | Subcategoria | Item | Qtd | Valor unit. (R$) | Valor total (R$) | Origem |
|------|-----------|--------------|------|-----|-------------------|-------------------|--------|
| 05/09 | Alimentação | Verduras/Legumes | Cebola | 0,165 kg | 8,99/kg | 1,48 | Supermercado Dalben |
| 05/09 | Alimentação | Frutas | Limão Tahiti | 0,425 kg | 14,99/kg | 6,37 | Supermercado Dalben |
| 05/09 | Alimentação | Carnes | Carne bovina (Coxão Duro) | 0,620 kg | 58,99/kg | 36,57 | Supermercado Dalben |
| 05/09 | Alimentação | Carnes | Frango a Passarinho | 0,228 kg | 31,99/kg | 7,29 | Supermercado Dalben |
| 06/09 | Alimentação | Verduras/Legumes | Batata | 0,775 kg | 8,99/kg | 6,97 | Supermercado Dalben |
| 06/09 | Alimentação | Carnes | Fraldinha | 0,648 kg | 65,99/kg | 42,76 | Supermercado Dalben |
| 06/09 | Alimentação | Bebidas | Refrigerante Coca-Cola 1,5L Zero | 1 un | 8,49 | 8,49 | Supermercado Dalben |
| 06/09 | Alimentação | Frutas | Banana Maçã | 0,470 kg | 9,99/kg | 4,70 | Supermercado Dalben |
| 06/09 | Alimentação | Padaria/Doces | Lua de Mel (recheado) | 0,124 kg | 61,99/kg | 7,69 | Supermercado Dalben |
| 06/09 | Alimentação | Grãos/Cereais | Feijão Camil Carioca 1kg | 1 un | 9,49 | 9,49 | Supermercado Dalben |
| 06/09 | Alimentação | Padaria/Doces | Goma de Mascar Trident 8g | 1 un | 3,69 | 3,69 | Supermercado Dalben |
| 10/09 | Alimentação | Padaria/Doces | Pão Pullman 480g Tradicional | 1 un | 8,48 | 8,48 | Supermercado Dalben |
| 10/09 | Alimentação | Bebidas | Refrigerante Coca-Cola 1,5L Zero | 1 un | 10,49 | 10,49 | Supermercado Dalben |
| 10/09 | Alimentação | Padaria/Doces | Lua de Mel (recheado) | 0,114 kg | 61,99/kg | 7,07 | Supermercado Dalben |
| 10/09 | Alimentação | Outros | Panqueca de Frios (tabela 45,99/kg, com desconto de R$7,91) | 0,344 kg | 22,99/kg | 7,91 | Supermercado Dalben |

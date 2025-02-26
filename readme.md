# **📌 Documentação Oficial do Marketplace**

## **1️⃣ Visão Geral**
O Marketplace é uma plataforma robusta e escalável onde usuários podem **comprar e vender produtos** de diversas categorias. 
Ele foi projetado para garantir **segurança, eficiência e interatividade**, utilizando tecnologias modernas.

### ** Tecnologias Utilizadas:**
- **Back-end:** Django com Django Templates (sem REST Framework)
- **Front-end:** Django Templates + Alpine.js + Tailwind CSS
- **Banco de Dados:** MySQL
- **Armazenamento de Arquivos:** AWS S3 / DigitalOcean Spaces
- **Autenticação:** JWT
- **Pagamentos:** Stripe, PayPal, MBWay, PIX
- **Mensagens em Tempo Real:** WebSockets com Django Channels
- **Infraestrutura:** Docker, AWS EC2, Nginx, Gunicorn


---

## **2️⃣ Estrutura do Projeto**
Para garantir uma organização profissional e escalável, o projeto será estruturado da seguinte forma:

📂 `marketplace/` *(Projeto principal)*  
 ├── 📂 `config/` *(Configurações globais do Django, incluindo settings.py, urls.py, wsgi.py)*  
 ├── 📂 `apps/` *(Todos os apps do projeto ficam aqui para melhor modularização)*  
 │   ├── 📂 `usuarios/` *(Gerenciamento de usuários e perfis de vendedores)*  
 │   ├── 📂 `produtos/` *(Gerenciamento de produtos e categorias)*  
 │   ├── 📂 `pedidos/` *(Carrinho de compras, pedidos e pagamentos)*  
 │   ├── 📂 `chat/` *(Mensagens entre usuários e suporte)*  
 │   ├── 📂 `avaliacoes/` *(Avaliação de produtos e vendedores)*  
 │   ├── 📂 `logistica/` *(Gerenciamento de frete e rastreamento de pedidos)*  
 ├── 📂 `templates/` *(Templates HTML do Django, organizados por app)*  
 ├── 📂 `static/` *(Arquivos CSS, JavaScript e imagens estáticas)*  
 ├── 📂 `media/` *(Arquivos de upload dos usuários, como fotos de produtos)*  
 ├── 📄 `manage.py` *(Comando principal do Django para gerenciar o projeto)*  


---

## **2️⃣ Estrutura do Banco de Dados**
### ** Usuários**
Armazena os dados dos usuários, incluindo compradores e vendedores.

| Campo               | Tipo        | Descrição |
|--------------------|------------|-----------|
| `id`              | UUID        | Identificador único |
| `nome`            | String      | Nome completo |
| `email`           | String (único) | E-mail para login |
| `senha`           | Hash        | Senha criptografada |
| `tipo_usuario`    | Enum (`comprador`, `vendedor`, `admin`) | Define o tipo de usuário |
| `foto_perfil`     | Image       | Foto do usuário |
| `telefone`        | String      | Número de telefone |
| `endereco`        | Text        | Endereço principal do usuário |
| `data_cadastro`   | DateTime    | Data do registro |

### ** Perfil do Vendedor**
Contém informações adicionais obrigatórias para vendedores.

| Campo              | Tipo        | Descrição |
|--------------------|------------|-----------|
| `id`              | UUID        | Identificador único |
| `usuario_id`      | ForeignKey (Usuario) | Relacionado ao usuário principal |
| `nif`            | String      | Número de Identificação Fiscal |
| `conta_bancaria`  | String      | IBAN ou dados bancários |
| `nome_loja`       | String      | Nome da loja do vendedor |
| `endereco_loja`   | Text        | Endereço físico (se aplicável) |
| `documento_verificacao` | File | Documento para verificação |
| `status_verificacao` | Enum (`pendente`, `aprovado`, `rejeitado`) | Status da análise do admin |
| `nivel_vendedor`  | Enum (`novo`, `verificado`, `top`) | Nível do vendedor |
| `data_cadastro`   | DateTime    | Data de ativação do modo vendedor |

### * 🔹 Categorias e Produtos**
Os produtos pertencem a categorias predefinidas.

| Campo        | Tipo        | Descrição |
|-------------|------------|-----------|
| `id`        | UUID       | Identificador único |
| `nome`      | String     | Nome da categoria |
| `descricao` | Text       | Descrição breve |
| `pai_id`    | ForeignKey (Categoria) | Permite criar subcategorias |

| Campo          | Tipo        | Descrição |
|---------------|------------|-----------|
| `id`          | UUID       | Identificador único |
| `nome`        | String     | Nome do produto |
| `descricao`   | Text       | Descrição detalhada |
| `preco`       | Decimal    | Valor do produto |
| `estoque`     | Integer    | Quantidade disponível |
| `status`      | Enum (`ativo`, `pausado`, `esgotado`, `removido`) | Estado do produto |
| `tipo_produto` | Enum (`novo`, `como novo`, `usado`) | Condição do item |
| `peso_kg`     | Decimal    | Peso para cálculo do frete |
| `vendedor_id` | ForeignKey (Usuario) | Dono do produto |
| `categoria_id` | ForeignKey (Categoria) | Categoria do produto |
| `data_criacao` | DateTime  | Data de publicação |

### **  Avaliações de Produtos e Vendedores**
Os usuários podem avaliar produtos e vendedores com base na experiência de compra.

| Campo         | Tipo        | Descrição |
|--------------|------------|-----------|
| `id`         | UUID       | Identificador único |
| `produto_id` | ForeignKey (Produto) | Produto avaliado |
| `comprador_id` | ForeignKey (Usuario) | Quem avaliou |
| `nota`       | Integer (1-5) | Avaliação de 1 a 5 estrelas |
| `comentario` | Text        | Comentário sobre o produto |
| `data_avaliacao` | DateTime | Data da avaliação |

| Campo         | Tipo        | Descrição |
|--------------|------------|-----------|
| `id`         | UUID       | Identificador único |
| `vendedor_id` | ForeignKey (Usuario) | Vendedor avaliado |
| `comprador_id` | ForeignKey (Usuario) | Quem avaliou |
| `nota`       | Integer (1-5) | Avaliação de 1 a 5 estrelas |
| `comentario` | Text        | Comentário sobre o vendedor |
| `data_avaliacao` | DateTime | Data da avaliação |

### ** Chat e Suporte**
Sistema de mensagens entre usuários e a equipe de suporte.

| Campo         | Tipo        | Descrição |
|--------------|------------|-----------|
| `id`         | UUID       | Identificador único |
| `remetente_id` | ForeignKey (Usuario) | Quem enviou a mensagem |
| `destinatario_id` | ForeignKey (Usuario) | Quem recebeu |
| `tipo_conversa` | Enum (`comprador-vendedor`, `suporte`) | Tipo de chat |
| `mensagem`  | Text       | Texto da mensagem |
| `data_envio` | DateTime  | Data da mensagem |

---

## **3️⃣ Segurança e Autenticação**
- **JWT para autenticação**
- **Proteção contra fraudes no checkout**
- **Moderação automática de produtos suspeitos**


---
**Esta documentação será atualizada conforme o projeto evolui!** 


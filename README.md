# 🐍 Aptoide Scraper API

API REST para extração de metadados de aplicativos da loja Aptoide. Esta API permite obter informações detalhadas sobre qualquer aplicativo Android disponível no Aptoide através de seu `package_name`.

## ✨ Funcionalidades

- ✅ **Extrai 14 campos** de metadados de aplicativos
- ✅ **Suporte a múltiplos apps** (Facebook, WhatsApp, Instagram, etc.)
- ✅ **Tratamento robusto de erros** (404 para apps não encontrados)
- ✅ **Performance otimizada** com requests assíncronos
- ✅ **Documentação automática** com Swagger UI
- ✅ **Validação de dados** com Pydantic
- ✅ **Logging detalhado** para debugging

## 🚀 Começando

### Pré-requisitos

- Python 3.9 ou superior
- pip (gerenciador de pacotes Python)

### Instalação

1. **Clone o repositório**
```bash
git clone <seu-repositorio>
cd aptoide-scraper
```

2. **Crie um ambiente virtual (recomendado)**
```bash
python -m venv venv
source venv/bin/activate  # Linux/Mac
# ou
venv\Scripts\activate     # Windows
```

3. **Instale as dependências**
```bash
pip install -r requirements.txt
```

### Executando a API

```bash
uvicorn app.main:app --reload
```

A API estará disponível em: `http://localhost:8000`

## 📚 Documentação da API

### Documentação Interativa
- **Swagger UI**: `http://localhost:8000/docs`
- **ReDoc**: `http://localhost:8000/redoc`

### Endpoints

#### `GET /aptoide`
Retorna metadados completos de um aplicativo específico.

**Parâmetros:**
- `package_name` (string, obrigatório): Nome do pacote do aplicativo Android

**Exemplo de Request:**
```bash
curl "http://localhost:8000/aptoide?package_name=com.facebook.katana"
```

**Exemplo de Response (200 OK):**
```json
{
  "name": "Facebook",
  "size": "145.3 MB",
  "downloads": "2B",
  "version": "541.0.0.85.79",
  "release_date": "2025-12-14 21:30:58",
  "min_screen": "SMALL",
  "supported_cpu": "arm64-v8a",
  "package_id": "com.facebook.katana",
  "sha1_signature": "CC:69:EF:02:CC:1D:98:0C:EB:FC:31:4D:E9:2E:CB:63:22:AD:29:FE",
  "developer_cn": "Meta Platforms Inc.",
  "organization": "Meta Platforms Inc.",
  "local": "Menlo Park",
  "country": "US",
  "state_city": "California"
}
```

**Códigos de Erro:**
- `404 Not Found`: Aplicativo não encontrado no Aptoide
- `400 Bad Request`: Parâmetro inválido ou ausente
- `500 Internal Server Error`: Erro interno no servidor

#### `GET /health`
Verifica o status da API.

**Response:**
```json
{
  "status": "healthy",
  "service": "aptoide-scraper"
}
```

## 🏗️ Arquitetura do Projeto

```
aptoide-scraper/
├── app/
│   ├── __init__.py
│   ├── main.py              # API FastAPI e endpoints
│   ├── scraper.py           # Lógica de scraping do Aptoide
│   └── schemas.py           # Modelos Pydantic para validação
├── tests/
│   └── test_scraper.py      # Testes do scraper
├── requirements.txt         # Dependências do projeto
└── README.md               # Esta documentação
```

### Módulos Principais

#### `app/main.py`
- Configuração da aplicação FastAPI
- Definição dos endpoints
- Tratamento centralizado de exceções
- Middleware de logging

#### `app/scraper.py`
- **Classe `AptoideScraper`**: Responsável por extrair dados do Aptoide
- **Estratégia híbrida**: Extrai dados primários do JSON embutido (`__NEXT_DATA__`) e complementa com HTML quando necessário
- **Formatação inteligente**: Converte bytes para MB, números para formato legível (2B, 1.5M, etc.)
- **Resiliência**: Múltiplas estratégias de fallback para garantir dados mesmo com mudanças no site

#### `app/schemas.py`
- **Classe `AppResponse`**: Schema Pydantic para validação e documentação automática
- Garante que a API sempre retorne os 14 campos exigidos

## 🔧 Tecnologias Utilizadas

- **FastAPI**: Framework web moderno e rápido com tipagem estática
- **HTTPX**: Cliente HTTP assíncrono para requests eficientes
- **BeautifulSoup4**: Parsing e extração de dados HTML
- **Pydantic**: Validação de dados e serialização
- **Uvicorn**: Servidor ASGI de alta performance

## 🎯 Decisões de Design

### 1. **Estratégia de Scraping Híbrida**
Optamos por priorizar a extração de dados do JSON embutido (`__NEXT_DATA__`) por ser:
- Mais rápido e eficiente que parsing HTML
- Menos suscetível a mudanças na estrutura do site
- Contém dados mais estruturados e completos

### 2. **Resiliência e Fallback**
O scraper implementa três camadas de resiliência:
1. Extração primária do JSON
2. Complementação com dados HTML quando necessário
3. Fallback completo para parsing HTML se o JSON não estiver disponível

### 3. **Formatação Inteligente**
- Downloads: Converte `2000000000` → `"2B"`
- Tamanho: Converte bytes → `"145.3 MB"`
- Data: Padroniza formato `YYYY-MM-DD HH:MM:SS`

### 4. **Tratamento de Erros Granular**
- `404`: App não existe no Aptoide
- `400`: Parâmetros inválidos
- `500`: Erros internos com logging detalhado

## 🧪 Testes

Execute os testes do scraper:
```bash
python -m tests.test_scraper
```

### Casos de Teste
- ✅ App existente (Facebook)
- ✅ Dados completos extraídos
- ✅ Formatação correta dos campos
- ✅ Resiliência a mudanças na estrutura

## 📊 Exemplos de Uso

### Testando Diferentes Apps
```bash
# Facebook
curl "http://localhost:8000/aptoide?package_name=com.facebook.katana"

# WhatsApp
curl "http://localhost:8000/aptoide?package_name=com.whatsapp"

# Instagram
curl "http://localhost:8000/aptoide?package_name=com.instagram.android"

# App inexistente (teste de erro)
curl "http://localhost:8000/aptoide?package_name=com.app.inexistente"
```

### Integração com Python
```python
import httpx
import asyncio

async def get_app_data(package_name: str):
    async with httpx.AsyncClient() as client:
        response = await client.get(
            "http://localhost:8000/aptoide",
            params={"package_name": package_name}
        )
        return response.json()

# Exemplo de uso
data = asyncio.run(get_app_data("com.facebook.katana"))
```


## 👥 Autor

- **Marcelo Ramos** 

## 🙏 Agradecimentos

- Equipe da Aptoide pelo desafio técnico inspirador
- Comunidade FastAPI pela documentação excelente
- Todos os mantenedores das bibliotecas open-source utilizadas

---

**🎯 Challenge Completo:** Esta solução atende a todos os requisitos do Python Developer Challenge da Aptoide, com código limpo, arquitetura escalável e documentação completa.

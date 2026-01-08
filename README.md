##🐍 Aptoide Scraper API
API para scraping de dados de apps do Aptoide Store.

## 🚀 Instalação

```bash
# Clone o repositório
git clone <seu-repositorio>

# Instale dependências
pip install -r requirements.txt

▶️ Execução

bash
uvicorn app.main:app --reload
📚 Endpoints

GET /aptoide

Retorna dados de um app específico.

Parâmetros:

package_name (query): Nome do pacote (ex: com.facebook.katana)
Exemplo:

bash
curl "http://localhost:8000/aptoide?package_name=com.facebook.katana"
Resposta (JSON):

json
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
GET /health

Verifica se a API está funcionando.

🏗️ Arquitetura

app/main.py - API FastAPI com endpoints
app/scraper.py - Scraper que extrai dados do Aptoide
app/schemas.py - Modelos Pydantic para validação
tests/test_scraper.py - Testes do scraper
🔧 Tecnologias

FastAPI
HTTPX (async requests)
BeautifulSoup4 (web scraping)
Pydantic (validação de dados)
text

### **4. Faça commit e push:**
```bash
git add .
git commit -m "API Aptoide Scraper completa com scraping de 14 campos"
git push origin main

# Dashboard de Qualidade — Streamlit

Dashboard interativo para monitoramento de indicadores de qualidade, construído com Python + Streamlit.

## 🗂️ Estrutura do Projeto

```
qualidade_streamlit/
├── app.py                  # Aplicação principal
├── requirements.txt        # Dependências Python
├── .env.example            # Template de variáveis de ambiente
├── .gitignore
├── .streamlit/
│   └── config.toml         # Tema e configurações do Streamlit
├── data/                   # Coloque seus arquivos de dados aqui
│   └── dados.xlsx          # (exemplo)
└── README.md
```

## ⚙️ Configuração do Ambiente

### 1. Criar e ativar o ambiente virtual

```powershell
# Criar o venv
py -m venv .venv

# Ativar (PowerShell)
.\.venv\Scripts\Activate.ps1

# Ativar (CMD)
.\.venv\Scripts\activate.bat
```

### 2. Instalar as dependências

```powershell
pip install -r requirements.txt
```

### 3. Configurar variáveis de ambiente

```powershell
# Copie o arquivo de exemplo
copy .env.example .env

# Edite o .env com seus dados
notepad .env
```

### 4. Adicionar seus dados

Coloque seu arquivo Excel ou CSV na pasta `data/`:

```
data/dados.xlsx   ← padrão (ou configure DATA_PATH no .env)
```

### 5. Rodar o dashboard

```powershell
streamlit run app.py
```

O dashboard abrirá automaticamente em `http://localhost:8501`.

## 📦 Dependências Principais

| Biblioteca | Uso |
|-----------|-----|
| `streamlit` | Framework do dashboard |
| `pandas` | Manipulação de dados |
| `numpy` | Cálculos numéricos |
| `plotly` | Gráficos interativos |
| `openpyxl` | Leitura de arquivos Excel |
| `python-dotenv` | Variáveis de ambiente |

## 🎨 Tema

O tema escuro está configurado em `.streamlit/config.toml`. Para alterar, edite esse arquivo.

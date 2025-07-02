
# 📈 Onitel Automação

Este projeto automatiza o download de relatórios do portal de gestão da Onitel, atualiza um banco de dados local SQLite com essas informações e disponibiliza uma interface web (via Flask) para consulta.

---

## 🚀 Visão geral do funcionamento

✅ **1. Automação de download (`zap.py`)**
- Usa Selenium para acessar o portal da Onitel, faz login automático e aplica filtros por data e assunto ("instalação").
- Lê o intervalo de datas do arquivo `config.txt`.
- Faz download do relatório em XLSX para a pasta `downloads`.

✅ **2. Processamento e banco de dados (`criar_db.py`)**
- Lê o arquivo `downloads/resultado.xlsx` baixado pela automação.
- Faz limpeza e conversão dos dados (SLA numérico, datas formatadas).
- Atualiza o banco SQLite `relatorios.db` inserindo apenas registros novos.

✅ **3. Interface web (`app.py`)**
- Usa Flask para disponibilizar uma página web onde é possível:
    - Ver a lista de relatórios atualizados.
    - Filtrar SLA positivo (>=90) ou negativo (<90).
- Retorna dados via endpoint JSON em `/api/data`.

✅ **4. Scheduler**
- (não incluído como script direto, mas a automação pode ser agendada via `cron`, `task scheduler` ou script Python `schedule` para rodar a cada X minutos).

---

## 🗂 Estrutura do projeto

```
onitel_automacao/
│
├── app.py             # Servidor Flask para exibir dados
├── criar_db.py        # Atualiza banco SQLite a partir do XLSX
├── zap.py             # Automação Selenium que baixa o XLSX
│
├── config.txt         # Datas inicial e final (DD/MM/AAAA)
├── requirements.txt   # Dependências Python do projeto
│
├── relatorios.db      # Banco de dados SQLite
├── downloads/
│   └── resultado.xlsx # Último relatório baixado
│
├── templates/
│   └── index.html     # (Não enviado, mas usado pelo Flask)
└── static/            # (Para CSS/JS, se desejar)
```

---

## ⚙️ Como usar

### 🔥 1. Instalar dependências
É recomendado usar um ambiente virtual.
```bash
python -m venv venv
source venv/bin/activate  # Linux / macOS
venv\Scripts\activate   # Windows

pip install -r requirements.txt
```

---

### 📝 2. Configurar intervalo de datas
Edite o arquivo `config.txt`:

```
02/07/2025
02/07/2025
```

Primeira linha = data inicial, segunda linha = data final.  
O script `zap.py` irá ler essas datas para filtrar o relatório no site.

---

### 🖱 3. Executar a automação de download
```bash
python zap.py
```
Ele abrirá o navegador Chrome, fará login, aplicará filtros e baixará o XLSX em `downloads/resultado.xlsx`.

---

### 🗄 4. Atualizar o banco de dados
```bash
python criar_db.py
```
Esse script lerá o arquivo XLSX, fará a limpeza dos dados e atualizará o banco `relatorios.db` com novos registros.

---

### 🌐 5. Rodar o servidor Flask
```bash
python app.py
```
- Acesse `http://127.0.0.1:5000` no navegador.
- Endpoint API: `http://127.0.0.1:5000/api/data?sla_filter=positivo` ou `sla_filter=negativo`.

---

## 🔄 Automatizando tudo
Para rodar de forma contínua (por exemplo a cada 5 minutos), pode-se criar um script Python ou usar agendadores:

### Usando `schedule` (Python)
```python
import schedule
import time
import os

def job():
    os.system("python zap.py")
    os.system("python criar_db.py")

schedule.every(5).minutes.do(job)

while True:
    schedule.run_pending()
    time.sleep(1)
```
---

## 🖥 Requisitos e observações
- Python 3.8+  
- Chrome instalado (o `zap.py` usa `webdriver_manager` para gerenciar o driver).

---

## 📦 Dependências
📋 Listadas em `requirements.txt`, principais:
- selenium, pandas, openpyxl, flask, sqlite3, schedule, cryptography, xlsxwriter, pystray, Pillow

---

## ✅ Conclusão
Este projeto foi criado para automatizar o processo manual de baixar relatórios do portal Onitel, garantir persistência via banco de dados e oferecer uma visualização centralizada via web.  

---

✍️ **Autor:** [Gabriel De Almeida Rocha]

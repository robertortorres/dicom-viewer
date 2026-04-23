# Sistema de Laudos DICOM 🫁

Sistema completo para visualização e geração de laudos médicos a partir de imagens DICOM.

## 🚀 Como rodar

### Pré-requisitos
- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/) (já vem junto com Docker Desktop)

### Subir o sistema

```bash
# Clone ou extraia o projeto
cd dicom-laudo

# Construir e iniciar
docker compose up --build

# Em background (produção)
docker compose up --build -d
```

Acesse: **http://localhost:8080**

### Parar
```bash
docker compose down
```

### Ver logs
```bash
docker compose logs -f
```

---

## 📁 Estrutura

```
dicom-laudo/
├── backend/
│   ├── app.py            # API Flask (Python)
│   ├── requirements.txt
│   └── Dockerfile
├── frontend/
│   ├── public/
│   │   └── index.html    # Interface completa (HTML/JS/CSS)
│   ├── nginx.conf
│   └── Dockerfile
├── docker-compose.yml
└── README.md
```

---

## ✨ Funcionalidades

### Imagens
- Upload de arquivos `.dcm` avulsos ou `.zip` com múltiplos arquivos
- Conversão correta via `pydicom` (windowing DICOM, CLAHE, normalização percentil)
- Controles de **brilho** e **contraste** em tempo real via slider
- Botão **Auto-melhorar** para preset de clareza automático
- Visualizador de imagem em tela cheia (clique na imagem)
- Navegação por teclado: ← → para navegar, Esc para fechar

### Laudo
- Preenchimento automático dos dados do paciente extraídos do DICOM:
  - Nome, sexo, data do exame, clínica, fabricante do equipamento
- Campos editáveis: médico, CRM, tipo de exame, achados, conclusão
- **Prévia visual** do documento ao lado do formulário (atualiza em tempo real)
- Geração de PDF profissional com:
  - Cabeçalho institucional colorido
  - Barra de dados do paciente
  - **2 imagens por folha** (paginação automática)
  - Seções de achados e conclusão
  - Linha de assinatura do médico

### Histórico
- Todos os laudos gerados são salvos no banco de dados **SQLite** (persistente via volume Docker)
- Listagem com paciente, exame, clínica, data e número de imagens
- Re-download do PDF de qualquer laudo anterior
- Exclusão de registros

---

## 🔧 Dados persistentes

Os dados (banco SQLite + PDFs gerados) ficam em um **Docker volume** chamado `dicom-data`.

Para fazer backup:
```bash
docker run --rm -v dicom-data:/data -v $(pwd):/backup alpine \
  tar czf /backup/backup-dicom.tar.gz /data
```

Para restaurar:
```bash
docker run --rm -v dicom-data:/data -v $(pwd):/backup alpine \
  tar xzf /backup/backup-dicom.tar.gz -C /
```

---

## 📦 Tecnologias

| Camada | Tecnologia |
|--------|-----------|
| Backend | Python 3.11, Flask, pydicom, Pillow, ReportLab |
| Frontend | HTML5, CSS3, JavaScript puro |
| Servidor web | Nginx (proxy reverso) |
| Banco de dados | SQLite (via volume Docker) |
| Container | Docker + Docker Compose |

---

## 🛠 Configurações avançadas

### Alterar a porta
Edite `docker-compose.yml`:
```yaml
ports:
  - "PORTA_DESEJADA:80"
```

### Aumentar tamanho máximo de upload
Edite `frontend/nginx.conf`:
```nginx
client_max_body_size 1G;
```

E `backend/app.py` (Gunicorn aceita arquivos grandes por padrão).

### Usar banco PostgreSQL (opcional)
Substitua o `sqlite3` no `backend/app.py` por `psycopg2` e configure a conexão.

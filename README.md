# Web Form Filling

Bot de RPA em Python que lê respostas de uma planilha Excel e as envia, uma a uma, para um Google Forms de pesquisa de satisfação de funcionários. Construído com o [BotCity Web](https://documentation.botcity.dev/tutorials/python-automations/web/) (Selenium) e integrável ao BotCity Maestro.

## Como funciona

1. Lê a planilha `Satisfaction.xlsx` com o pandas.
2. Abre o formulário no Chrome.
3. Para cada linha da planilha, preenche os campos, envia a resposta e clica em "Enviar outra resposta".
4. Fecha o navegador e reporta o resultado ao Maestro (quando conectado).

### Campos do formulário

| Coluna da planilha  | Campo no formulário    | Tipo                |
|---------------------|------------------------|---------------------|
| `Employee Name`     | Employee name          | Texto               |
| `Years of Service`  | Years of Service       | Texto               |
| `Department`        | Department             | Lista suspensa      |
| `Satisfaction Rating` | Employee satisfaction | Escala (opções)     |

## Requisitos

- Python 3.8+
- Google Chrome e o [ChromeDriver](https://googlechromelabs.github.io/chrome-for-testing/) compatível com a sua versão
- Windows (os caminhos padrão do projeto são de Windows)

## Instalação

```bash
git clone <url-do-repositorio>
cd Web-Form-Filling
python -m venv .venv
.venv\Scripts\activate
pip install --upgrade -r requirements.txt
```

## Configuração

Os valores abaixo estão fixos em [bot.py](bot.py) e devem ser ajustados ao seu ambiente:

| Item                  | Padrão                                            | Onde alterar                  |
|-----------------------|---------------------------------------------------|-------------------------------|
| Caminho do ChromeDriver | `C:\chromedriver-win64\chromedriver.exe`        | `bot.driver_path`             |
| Planilha de entrada   | `C:\Projects\Satisfaction.xlsx`                   | `pd.read_excel(...)`          |
| URL do formulário     | Google Forms de exemplo                           | `bot.browse(...)`             |
| Modo headless         | `False` (navegador visível)                       | `bot.headless`                |

### Formato da planilha

A planilha precisa ter exatamente estas colunas:

| Employee Name | Years of Service | Department | Satisfaction Rating |
|---------------|------------------|------------|---------------------|
| Maria Silva   | 5                | Sales      | 4                   |

Os valores de `Department` e `Satisfaction Rating` devem corresponder exatamente às opções existentes no formulário.

> **Atenção:** os seletores e os textos dos botões ("Enviar", "Enviar outra resposta") assumem um formulário com interface em **português**. Se o seu formulário estiver em outro idioma, ajuste os XPaths em [bot.py](bot.py).

## Uso

```bash
python bot.py
```

Executado localmente, o bot funciona sem o Maestro: `BotMaestroSDK.RAISE_NOT_CONNECTED = False` evita erros quando não há conexão.

## Empacotamento para o BotCity Maestro

Gera o `WebFormFilling.zip` pronto para upload no Maestro:

```bash
# Windows (PowerShell)
./build.ps1

# Windows (cmd)
build.bat

# Linux / macOS
./build.sh
```

## Estrutura do projeto

```
.
├── bot.py                 # Lógica principal do bot
├── requirements.txt       # Dependências
├── WebFormFilling.botproj # Metadados do projeto BotCity
├── build.bat | .ps1 | .sh # Scripts de empacotamento
└── resources/             # Recursos do bot (imagens)
```

## Tecnologias

- [BotCity Web Framework](https://documentation.botcity.dev/)
- [BotCity Maestro SDK](https://documentation.botcity.dev/maestro/)
- [pandas](https://pandas.pydata.org/) e [openpyxl](https://openpyxl.readthedocs.io/)

## Solução de problemas

- **`ModuleNotFoundError: No module named 'botcity'`**: o interpretador em uso não é o mesmo em que as dependências foram instaladas. Reinstale com `pip install --upgrade -r requirements.txt` no interpretador correto.
- **Elemento não encontrado**: o layout do Google Forms mudou ou o idioma é diferente; revise os XPaths.
- **Erro de versão do driver**: a versão do ChromeDriver precisa ser compatível com a do Chrome instalado.

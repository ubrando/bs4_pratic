# Revisão de Bibliotecas 

## Selenium

```python
# Importar bibliotecas
from selenium import webdriver
from selenium.webdriver.common.by import By
import time

# Abre o navegador Chrome e navega para o url especificado
driver = webdriver.Chrome()
driver.get("https://books.toscrape.com/")

# Espera a página terminar de carregar
time.sleep(2)

# Inicia uma lista de books vazia 
books = []

# Cria uma lista de items, procurando pela class = "product_pod"
# Percorre a linta de items e para cada item dentro da lista ele tenta achar o titulo e o preço, adicionando na lista de livros
# Acha o botão de avançar na página pela class = "next" com name = "a", simula o click no botão, aguarda a página carregar e vai para a proxima página repetindo o processo
for page in range(1, 3):
    items = driver.find_elements(By.CLASS_NAME, "product_pod")

    for item in items:
        title = item.find_element(By.TAG_NAME, "h3").text
        price = item.find_element(By.CLASS_NAME, "price_color").text
        books.append((title, price))

    next_button = driver.find_element(By.CLASS_NAME, "next").find_element(By.TAG_NAME, "a")
    next_button.click()
    time.sleep(2)

# Printa a lista de books
for book in books:
    print(book)

# Fecha o navegador
driver.quit()
```

### Resposta Revisada

1. Abre o navegador Chrome e acessa o site https://books.toscrape.com/.
2. Aguarda 2 segundos para garantir que a página carregue.
3. Inicializa uma lista vazia books para armazenar os títulos e preços dos livros.
4. Percorre 2 páginas (primeira e segunda) coletando os dados dos livros:
5. Encontra todos os itens com a classe product_pod (que representam os livros).
6. Para cada livro, captura o título (h3) e o preço (price_color).
7. Adiciona essas informações na lista books.
8. Encontra o botão "Next" e clica para avançar para a próxima página.
9. Espera 2 segundos para garantir que a nova página carregue antes de continuar.
10. Após coletar os dados das duas páginas, imprime a lista books.
11. Fecha o navegador.

### Sugestões de Melhorias
✅ Se quiser que o código continue até a última página, ao invés de limitar range(1, 3), poderia usar um loop while para verificar se o botão "Next" ainda existe:

```python
while True:
    items = driver.find_elements(By.CLASS_NAME, "product_pod")

    for item in items:
        title = item.find_element(By.TAG_NAME, "h3").text
        price = item.find_element(By.CLASS_NAME, "price_color").text
        books.append((title, price))

    try:
        next_button = driver.find_element(By.CLASS_NAME, "next").find_element(By.TAG_NAME, "a")
        next_button.click()
        time.sleep(2)
    except:
        break  # Sai do loop se o botão "Next" não for encontrado
```
1️⃣ Interação com Elementos Dinâmicos
Esperas Explícitas vs Implícitas: Em vez de usar time.sleep(), aprenda a utilizar WebDriverWait e expected_conditions. Isso permite que você espere até que um elemento esteja presente ou visível, o que melhora a performance e a robustez do seu código.

```python
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By

WebDriverWait(driver, 10).until(EC.presence_of_element_located((By.ID, "someId")))
```

2️⃣ Manejo de Pop-ups e Janelas
Janela Modal: Muitos sites exibem pop-ups ou modais que exigem interação (ex: clique para fechar ou aceite de cookies). É bom saber como lidar com isso.

```python
# Aceitando um pop-up de alerta
alert = driver.switch_to.alert
alert.accept()
```

Múltiplas Janelas: Caso o site abra novas janelas ou abas, saber alternar entre elas é fundamental.

```python
driver.switch_to.window(driver.window_handles[1])  # Switch para a segunda janela
```

3️⃣ Captura de Tela
Screenshots: Caso precise capturar a tela do navegador em determinado momento, use:

```python
driver.save_screenshot("screenshot.png")
```

4️⃣ Formulários e Interação com Campos
Preenchendo formulários: Interagir com campos de texto, botões de rádio, checkboxes, etc.

```python
input_element = driver.find_element(By.NAME, "q")
input_element.send_keys("search query")
input_element.submit()
```

5️⃣ XPath e CSS Selectors
* Aprofundamento em Localizadores: Enquanto find_element(By.CLASS_NAME) e find_element(By.ID) são comuns, XPath e CSS Selectors são essenciais para localizar elementos mais complexos.

* CSS Selector: driver.find_element(By.CSS_SELECTOR, ".className > input")
* XPath: driver.find_element(By.XPATH, "//div[@class='className']/input")
* 
6️⃣ Estrutura de Testes com Selenium
Integração com Frameworks de Teste: Selenium se integra bem com frameworks como PyTest, unittest, e Robot Framework para automação de testes.

Exemplo com unittest:
```python
import unittest
from selenium import webdriver

class TestSearch(unittest.TestCase):
    def setUp(self):
        self.driver = webdriver.Chrome()

    def test_search_python(self):
        self.driver.get("https://www.python.org")
        search_box = self.driver.find_element(By.NAME, "q")
        search_box.send_keys("Selenium")
        search_box.submit()
        self.assertIn("Selenium", self.driver.page_source)

    def tearDown(self):
        self.driver.quit()

if __name__ == "__main__":
    unittest.main()
```

7️⃣ Scraping Responsável
Legalidade e Ética: É sempre bom lembrar que o scraping de websites deve ser feito de forma ética, respeitando as regras do site (geralmente indicadas nos robots.txt). Evite sobrecarregar servidores com requisições excessivas.

```python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.common.action_chains import ActionChains
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
import time

# Setup do WebDriver
driver = webdriver.Chrome()
driver.get("http://automationpractice.com/index.php")

# 1. Acessar o site e realizar login
driver.find_element(By.CLASS_NAME, "login").click()
WebDriverWait(driver, 10).until(EC.visibility_of_element_located((By.ID, "email")))

driver.find_element(By.ID, "email").send_keys("testuser@example.com")
driver.find_element(By.ID, "passwd").send_keys("testpassword123")
driver.find_element(By.ID, "SubmitLogin").click()

# 2. Pesquisar por um produto
WebDriverWait(driver, 10).until(EC.visibility_of_element_located((By.ID, "search_query_top")))
search_box = driver.find_element(By.ID, "search_query_top")
search_box.send_keys("T-shirt")
search_box.send_keys(Keys.RETURN)

# 3. Selecionar o primeiro produto e adicionar ao carrinho
WebDriverWait(driver, 10).until(EC.visibility_of_element_located((By.CLASS_NAME, "product_img_link")))
first_product = driver.find_element(By.CLASS_NAME, "product_img_link")
first_product.click()

# Adicionar ao carrinho
WebDriverWait(driver, 10).until(EC.element_to_be_clickable((By.NAME, "Submit"))).click()

# 4. Validar carrinho e checkout
WebDriverWait(driver, 10).until(EC.visibility_of_element_located((By.CLASS_NAME, "shopping_cart")))
driver.find_element(By.CLASS_NAME, "shopping_cart").click()

WebDriverWait(driver, 10).until(EC.visibility_of_element_located((By.CLASS_NAME, "button-medium")))
driver.find_element(By.CLASS_NAME, "button-medium").click()

# 5. Finalizar a compra
WebDriverWait(driver, 10).until(EC.visibility_of_element_located((By.NAME, "processAddress")))
driver.find_element(By.NAME, "processAddress").click()

WebDriverWait(driver, 10).until(EC.visibility_of_element_located((By.NAME, "processCarrier")))
driver.find_element(By.NAME, "processCarrier").click()

# 6. Escolher método de pagamento e finalizar
WebDriverWait(driver, 10).until(EC.element_to_be_clickable((By.CLASS_NAME, "bankwire"))).click()

# Capturar a tela de confirmação
driver.save_screenshot("compra_finalizada.png")

# 7. Fechar o navegador
time.sleep(5)
driver.quit()
```
### Funções impotantes:

- webdriver.Chrome(): Inicializa o navegador Chrome.
- webdriver.Firefox(): Inicializa o navegador Firefox.
- webdriver.Edge(): Inicializa o navegador Edge.
- get(url): Abre a URL fornecida no navegador.
- WebDriverWait(driver, timeout).until(condition): Espera até que a condição seja atendida (por exemplo, até um elemento estar presente ou visível).
- EC.presence_of_element_located((By.ID, "element_id")): Espera até que o elemento com o ID fornecido esteja presente na página.
- find_element_by_id(): Localiza um elemento pelo ID.
- find_element_by_name(): Localiza um elemento pelo nome.
- find_element_by_xpath(): Localiza um elemento com XPath.
- find_element_by_css_selector(): Localiza um elemento pelo seletor CSS.
- find_element_by_class_name(): Localiza um elemento pela classe.
- click(): Clica no elemento.
- send_keys(text): Envia texto para um campo de entrada (como um formulário de login).
- clear(): Limpa o conteúdo de um campo de texto.
- submit(): Envia um formulário.
- back(): Volta para a página anterior no histórico.
- forward(): Avança para a próxima página no histórico.
- refresh(): Atualiza a página atual.
- switch_to.window(): Troca para uma janela de navegador específica.
- switch_to.frame(): Muda para um iframe dentro da página.
- get_text(): Obtém o texto do elemento.
- get_attribute(attribute): Obtém o valor de um atributo do elemento.
- close(): Fecha a aba atual do navegador.
- quit(): Encerra a sessão do navegador e fecha todas as janelas associadas

## Requests
```python
# Importa as bibliotecas necessárias
import requests
import json

# Define a URL da API de posts fictícios
url = "https://jsonplaceholder.typicode.com/posts"

# Faz uma requisição GET para obter os dados da URL
response = requests.get(url)

# Verifica se a requisição foi bem-sucedida (código 200)
if response.status_code == 200:
    # Converte a resposta para um objeto JSON (lista de dicionários)
    data = response.json()
    
    # Inicializa uma lista vazia para armazenar os posts filtrados
    posts = []

    # Percorre os primeiros 5 posts da resposta
    for post in data[:5]:
        # Adiciona um dicionário contendo apenas os campos desejados
        posts.append({
            "id": post["id"],
            "title": post["title"],  # Corrigido erro de digitação (title)
            "body": post["body"]
        })

    # Abre (ou cria) um arquivo JSON para salvar os dados coletados
    with open("posts.json", "w", encoding="utf-8") as file:
        json.dump(posts, file, indent=4, ensure_ascii=False)  # Formata o JSON para melhor legibilidade

    # Mensagem de sucesso após salvar os dados
    print("Dados salvos com sucesso no arquivo 'posts.json'.")
else:
    # Se houver erro na requisição, imprime o código de status
    print(f"Erro: {response.status_code}")
```
```python
# Importa as bibliotecas necessárias
import requests
import json

# Define a URL base da API
BASE_URL = "https://jsonplaceholder.typicode.com"

# Função para obter dados da API
# Recebe um endpoint como parâmetro, faz uma requisição GET e retorna os dados em formato JSON se a resposta for 200 (OK)
def get_data(endpoint):
    response = requests.get(f"{BASE_URL}/{endpoint}")
    return response.json() if response.status_code == 200 else None

# Obtém os 5 primeiros posts da API
posts = get_data("posts")[:5]
# Obtém todos os comentários da API
comments = get_data("comments")

# Verifica se os dados foram obtidos corretamente
if posts and comments:
    structured_data = []  # Lista para armazenar os dados estruturados

    # Percorre a lista de posts obtidos
    for post in posts:
        # Filtra os comentários que pertencem ao post atual
        post_comments = [comment for comment in comments if comment["postId"] == post["id"]]

        # Adiciona um dicionário estruturado à lista
        structured_data.append({
            "id": post["id"],
            "title": post["title"],
            "body": post["body"],
            "comments": [
                {
                    "name": c["name"],
                    "email": c["email"],
                    "body": c["body"]
                } 
                for c in post_comments
            ]
        })

    # Salva os dados estruturados em um arquivo JSON
    with open("posts_with_comments.json", "w", encoding="utf-8") as file:
        json.dump(structured_data, file, indent=4, ensure_ascii=False)

    print("Dados salvos com sucesso no arquivo 'posts_with_comments.json'.")
else:
    print("Erro ao obter dados da API.")
```
### Funções Requests

- requests.get(url, params=None, **kwargs): Realiza uma requisição HTTP GET. Usado para buscar dados de um servidor.
- requests.post(url, data=None, json=None, **kwargs)
- requests.put(url, data=None, **kwargs)
- requests.delete(url, **kwargs)
- requests.head(url, **kwargs)
- requests.options(url, **kwargs)
- requests.get(url, auth=('usuario', 'senha'))
- response.status_code: Código de status da resposta HTTP (ex: 200, 404).
- response.text: Conteúdo da resposta como texto (geralmente HTML, JSON, ou XML).
- response.json(): Converte a resposta JSON em um dicionário Python (se o conteúdo for JSON)
- response.content: Conteúdo da resposta como bytes. Usado para arquivos binários, como imagens ou PDFs.
- response.headers: Cabeçalhos HTTP da resposta.
- response = requests.get("https://api.exemplo.com/dados", timeout=5)
- allow_redirects: Se False, os redirecionamentos HTTP não são seguidos.
## BeautifulSoup

```python
import requests
from bs4 import BeautifulSoup
import json

# Definição da URL base
BASE_URL = "https://books.toscrape.com/catalogue/category/books/science_22/index.html"

# Função para obter a sopa de uma página
def get_soup(url):
    response = requests.get(url)
    if response.status_code == 200:
        return BeautifulSoup(response.text, "html.parser")
    else:
        print(f"Erro ao acessar a página: {response.status_code}")
        return None

# Função para extrair informações de livros de uma página
def extract_books_from_page(soup):
    books = []
    articles = soup.find_all("article", class_="product_pod")
    for article in articles:
        title = article.find("h3").find("a")["title"]
        price = article.find("p", class_="price_color").text
        rating = article.find("p", class_="star-rating")["class"][1]
        books.append({"title": title, "price": price, "rating": rating})
    return books

# Função para obter o link da próxima página
def get_next_page_url(soup):
    next_button = soup.find("li", class_="next")
    if next_button:
        next_page = next_button.find("a")["href"]
        return BASE_URL.replace("index.html", "") + next_page
    else:
        return None

# Lista para armazenar todos os livros
all_books = []

# URL inicial
current_url = BASE_URL

# Loop para navegar pelas páginas
while current_url:
    print(f"Buscando livros na página: {current_url}")
    
    # Obter a sopa da página atual
    soup = get_soup(current_url)
    
    if soup:
        # Extrair livros da página
        books_on_page = extract_books_from_page(soup)
        all_books.extend(books_on_page)
        
        # Verificar a próxima página
        current_url = get_next_page_url(soup)
    else:
        break

# Salvar os dados extraídos em um arquivo JSON
with open("books_science.json", "w", encoding="utf-8") as file:
    json.dump(all_books, file, indent=4, ensure_ascii=False)

print("Dados salvos com sucesso no arquivo 'books_science.json'.")
```

```python
import requests
import pandas as pd
from selenium import webdriver
from bs4 import BeautifulSoup
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.options import Options
from webdriver_manager.chrome import ChromeDriverManager
import time

# Configurações do Selenium para não abrir o navegador visivelmente
chrome_options = Options()
chrome_options.add_argument("--headless")  # Roda o Chrome de forma invisível
chrome_options.add_argument("--no-sandbox")
chrome_options.add_argument("--disable-dev-shm-usage")

# Inicia o WebDriver com o Selenium
driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()), options=chrome_options)

# Defina a URL de scraping
url = "https://quotes.toscrape.com/js/"  # Exemplo de página com conteúdo carregado via JavaScript

# Navega até a página
driver.get(url)

# Aguardar 2 segundos para garantir que o JavaScript seja executado
time.sleep(2)

# Pegue o conteúdo da página com o Selenium
page_source = driver.page_source

# Usando BeautifulSoup para analisar o HTML
soup = BeautifulSoup(page_source, "html.parser")

# Encontre todos os elementos com a classe 'quote'
quotes = soup.find_all('div', class_='quote')

# Extrair as informações de cada quote (texto e autor)
quote_data = []

for quote in quotes:
    text = quote.find('span', class_='text').get_text()
    author = quote.find('small', class_='author').get_text()
    tags = [tag.get_text() for tag in quote.find_all('a', class_='tag')]
    
    quote_data.append({
        'Text': text,
        'Author': author,
        'Tags': ", ".join(tags)
    })

# Converter para DataFrame do pandas
df = pd.DataFrame(quote_data)

# Salvar o DataFrame em um arquivo CSV
df.to_csv('quotes.csv', index=False)

# Mostrar o DataFrame no console
print(df)

# Fechar o driver do Selenium
driver.quit()
```
### Funções Requests
- BeautifulSoup(html, parser)
- soup.find(tag, attributes, recursive, string)
- soup.find_all(tag, attributes, recursive, string)
- soup.select(selector)
- soup.select_one(selector)
- soup.find_parent(tag)
- soup.find_all_previous(tag): Retorna todos os elementos anteriores a um determinado
- element.text: Obtém o texto dentro do elemento, removendo as tags HTML.
- element.get_text(): Outra forma de obter o texto de um elemento, com mais opções de manipulação.
- element["attribute"]: Acessa o valor de um atributo do elemento.
- element.attrs: Retorna um dicionário com todos os atributos do elemento.
- element['attribute'] = value: Modifica o valor de um atributo de um elemento.
- element.insert_before(new_element): Insere um novo elemento antes do elemento atual.
- element.insert_after(new_element): Insere um novo elemento depois do elemento atual.
- element.decompose(): Remove um elemento do DOM, incluindo todos os seus filhos.
- element.unwrap(): Remove o elemento, mas mantém seus filhos dentro do seu elemento pai.
- soup.new_tag(name, attrs): Cria uma nova tag.
- element.select(selector): Permite usar seletores CSS para encontrar elementos.
- element.select_one(selector): Retorna o primeiro elemento que corresponde ao seletor CSS.
- soup.prettify(): Retorna uma versão formatada e indentada do HTML, facilitando a leitura do conteúdo da página.

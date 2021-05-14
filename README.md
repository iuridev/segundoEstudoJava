# segundoEstudoJava

**IDE Utilizada é o Eclipse Java EE - com servidor Tomcat**


[link das anotações de estudo](https://www.notion.so/ESTUDOS-DE-JAVA-WEB-c0f779aeedd54cb0ab1b9812e6029b29)

Porta padrão do Tomcat : 8080

ex:[](http://localhost:8080/)[http://localhost:8080/](http://localhost:8080/)

O Context Root é como o projeto será chamando na URL

ex:[](http://localhost:8080/)[http://localhost:8080/](http://localhost:8080/)estudoJava → onde o "estudoJava é o context root.

[Apache HTTP ou Apache Tomcat?](https://www.notion.so/Apache-HTTP-ou-Apache-Tomcat-9e9dabf7ac894045b6bd3c2ab093c232)


## Servlet

É uma classe que `extends` o `servlet`, como trabalharemos com protocolo HTTP, estenderemos a `HttpServlet`

No **servlet** nunca devemos sobrescrever o método `service` (pq? ainda não sei responder rs), caso precise, devemos sobrescrever os métodos `doGet` e `doPost`. Lembrando que para sobrecrever métodos usamos a annotation `@Override.`

### Annotation

-   `@WebServlet(urlPatterns="/oi")` → Annotation para definir o nome do Servlet na URL que utilizaremos no navegador.

### Response (Resposta)

No mundo HTTP existem duas formas de devolver uma resposta: a primeira é definir um fluxo binário para, por exemplo, gerar um relatório, arquivo em PDF ou gráfico e devolver essa imagem. Para este caso, utilizaríamos o método getOutPutStream(). A segunda alternativa é utilizar o método getWriter() para devolver um conteúdo HTML.

Ex:

`@Override protected void service(HttpServletRequest req, **HttpServletResponse resp**) throws IOException { **PrintWriter out = resp.getWriter();** **out.println**("<html>"); out.println("<body>"); out.println("oi mundo, parabens vc escreveu o primeiro servlets."); out.println("</body>"); out.println("</html>"); }`

É importante frisar que o `out.println()` não imprime no console, diferentemente do `System.out.println()`, esse ultimo imprime no console.

### Request

Pode-se passar parâmetros pela URL, depois do `nome` da URL, inserimos o caractere `?` que marcará o início do parâmetro, seguido de seu `valor`. Vejamos um exemplo hipotético, cujo nome do parâmetro será `nome` e o valor `Iuri`. Para adicionarmos mais um parâmetro adicionaremos o caractere `&`, seguindo do `cnpj` (nome do segundo parâmetro) e o valor `123`.

`localhost:8080/gerenciador/novaEmpresa?nome=Iuri&cnpj=123`

Para recuperar os valores passados como parâmetros na URL, usa-se o `getParameter("String")` onde passamos como parâmetro desse método o nome da variável na URL.

```java
@WebServlet("/novaEmpresa")
public class NovaEmpresaServlet extends HttpServlet {

    private static final long serialVersionUID = 1L;

    protected void service(HttpServletRequest request, HttpServletResponse response) throws Serlvet {
        System.out.println("Cadastrando nova empresa");
        String nomeEmpresa = request.getParameter("nome");
        PrintWriter out = response.getWriter();
        out.println("<html><body>Empresa " + nomeEmpresa + " cadastrada com sucesso!</body></html>");
    }
}

```

O método `getParameter("String")` sempre irá retornar uma **String**, e recebe como parâmetro o nome do parâmetro recebido na requisição.

### Porém nem todas as informações pode está visíveis na URL, nesse caso como proceder?

Nesse caso deve-se sempre usar o método POST, e deixar o método GET apenas para consultas e não cadastro.

O método post está presente em um formulário por exemplo, com o `form` com o `method="post"`, cuidado, pois por padrão o `method` tem valor `get`.

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="ISO-8859-1">
		<title>Cadastro de Usuario</title>
	</head>
	<body>
		<form action="/segundoEstudoJava/OiEstudo" method="post">
			Nome: <input type="text" name="nome" />
			<input type="submit" />
		</form>
	</body>
</html>

```



1.  GET foi pensado para acessar algum recurso.
    
2.  POST foi pensado para alterar ou criar algum recurso.
    
3.  GET gera problemas quando precisamos enviar muitas informações.
    

## Porque não sobrescrever o método Service?

Quando usamos o `service`, a pagina pode reconhecer tanto o `get` quanto o `post`, quando estamos trabalhando com login isso é um problema, pois nossa aplicação só deveria aceitar o `POST`, em vista disso começaremos a sobrescrever o método `doGet` e `doPost`.

Alterando o código para:

```java
protected void **doPost**(**HttpServletRequest** request, **HttpServletResponse** response)
throws ServletException, IOException {
		System.out.println("Cadastrando nova empresa");
    String nomeEmpresa = request.getParameter("nome");
    PrintWriter out = response.getWriter();
    out.println("<html><body>Empresa " + nomeEmpresa + " cadastrada com sucesso!</body></html>");
}

```

## Uso de classe

O uso de classes é comum na construção de objetos, por exemplo no cadastro de uma nova empresa.

```java
public class Empresa {
	private int id;
	private String nome;

	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	public String getNome() {
		return nome;
	}
	public void setNome(String nome) {
		this.nome = nome;
	}
}

```

Assim no servlet construímos o objeto:

```java
protected void doPost(HttpServletRequest request, HttpServletResponse response) 
	throws ServletException, IOException {
		String nomeEmpresa = request.getParameter("nome");
		**Empresa empresa = new Empresa();
		empresa.setNome(nomeEmpresa);**
		
		PrintWriter out = response.getWriter();
        out.println("<html>"
        			+ "<body>"
		        		+ "Empresa " 
		        		**+ empresa.getNome()** 
		        		+ " cadastrada com sucesso!"
		        	+ "</body>"
		        + "</html>");
				
	}

```

A Classe tem um método estático que é executado quando a classe é carregada:

```java
static {
		//code ...
	}

```

# JSP → java server page

O problema quando usamos o servlet é quando precisamos fazer uso da linguagem HTML, como pode ser visto anteriormente para imprimir o HTML para o browser usamos o `out.println("")`, imagina em uma pagina completa, quantos comandos de **out.println** iriamos usar? para isso temos uma solução, fazer uso do JSP.

Neste novo formato podemos inserir códigos **Java**, desde que eles sejam marcados por `<% %>`. Chamamos essa inserção de código Java um arquivo jsp de **scriptlet**.

Por exemplo:

```html
<%
    String nomeEmpresa = "Alura";
    System.out.println(nomeEmpresa);
%>
<html><body>
<H1>Empresa <% out.println(nomeEmpresa); %> cadastrada com sucesso!</H1>
</body></html>

```

A instrução `<% out.println(nomeEmpresa) %>` pode ser abreviada para `<%= nomeEmpresa %>`

Alternativa correta! Todo o código (scriptlet) é executado no servidor antes de enviar a resposta para o navegador.

Agora que entendemos o que é Servlet e JSP, precisamos ligar os dois, pois o formulário na pagina HTML chama o servlet que chama o JSP com a resposta.

Para executarmos esse processo usaremos a `request`, que possui um objeto específico para despachar a requisição para o JSP, trata-se do `getRequestDispatcher()`. Precisamos indicar ao despachador o destino da requisição, isto é, o arquivo `/pagina.jsp`. Nos será devolvido um `RequestDispatcher`, portanto pegaremos a referência `rd` e realizaremos a importação.

```java
RequestDispatcher rd = request.getRequestDispatcher("/pagina.jsp");

```

Resta adicionarmos um item que ativa o percurso dessa requisição, o método `forward()` que receberá os parâmetros de `request` e `response`. É como se fosse um motoboy que leva os dados para um novo destino, no nosso caso o JSP.

```java
rd.forward(request, response);

```

Podemos passar atributos no request para o JSP, Para isso utilizaremos `setAttribute("empresa", empresa.getNome())`, contudo perceba que assinatura do método `setAttribute()` recebe um `string` name e um `Object`. A string name funciona como **apelido**, que será empresa e em seguida o **objeto** empresa.

```java
request.setAttribute("empresa", empresa.getNome());

```

Esse método tem como resposta um objeto, porém podemos converte-lo para string no **scriptlet** no **JSP**:

```java
<%
//scriptlet
String nomeEmpresa = (String)request.getAttribute("empresa");
System.out.println(nomeEmpresa);
%>

```

## Imports no JSP

para fazer **import** no JSP e fazer uso de métodos de algumas libs, usamos anotações `<%@ page import= "" %>` :

```java
<%@ page import="java.util.List, br.com.alura.gerenciador.servlet.Empresa"%>

```

# Porém desenvolver regras de negócio no scriptlet não é uma boa prática de programação

![https://media.giphy.com/media/L2wfaqVluXNOqwWNne/giphy.gif](https://media.giphy.com/media/L2wfaqVluXNOqwWNne/giphy.gif)

Para isso, usaremos uma outra linguagem bastante simples - mais precisamente de uma sintaxe, marcada por `${ }`, e utilizada para definir **expressões**. Uma expressão escrita entre as chaves { } é interpretada pelo navegador e impressa na tela. Porém expressões não fazem loops.

O Seguinte código:

```java
<%
 //scriptlet
 String nomeEmpresa = (String)request.getAttribute("empresa");
 System.out.println(nomeEmpresa);
%>

<html>
    <body>
        Empresa <%= nomeEmpresa  %> cadastrada com sucesso!
    </body>
</html>

```

pode ser substituído usando expressões :

```html
<html>
    <body>
        Empresa **${ empresa }** cadastrada com sucesso!
    </body>
</html>

```

# Classe-de-Contato-JavaBeans



Crie	a	classe	de		Contato		no	pacote		br.com.caelum.jdbc.modelo	.	Ela	será	nosso	JavaBean	para
representar	a	entidade	do	banco.	Deverá	ter	id,	nome,	email,	endereço	e	uma	data	de	nascimento.
Coloque	os	atributos	na	classe	e	gere	os	getters	e	setters	para	cada	atributo:


		public class Contato	{
				private	Long	id;
				private	String	nome;
				private	String	email;
				private	String	endereco;
				private	Calendar	dataNascimento;
        }
        
        
Dica:	use	o	atalho	do	Eclipse	para	gerar	os	getters	e	setters.	Aperte		Ctrl	+	3	,	digite		ggas		que	é	a
abreviação	de		Generate	getters	and	setters		e	selecione	todos	os	getters	e	setters.

Vamos	 desenvolver	 nossa	 classe	 de	 DAO.	 Crie	 a	 classe	 	 ContatoDao	 	 no	 pacote
	br.com.caelum.jdbc.dao	.	 Seu	 papel	 será	 gerenciar	 a	 conexão	 e	 inserir	 Contatos	 no	 banco	 de
dados.
Para	a	conexão,	vamos	criá-la	no	construtor	e	salvar	em	um	atributo:


		public class ContatoDao	{
				//	a	conexão	com	o	banco	de	dados
				private	Connection	connection;
        public	ContatoDao() {
						this.connection	=	new	ConnectionFactory().getConnection();
				}
		}
    
    
Use	o	Eclipse	para	ajudar	com	os	imports!	(atalho	Ctrl	+	Shift	+	O)
O	 próximo	 passo	 é	 criar	 o	 método	 de	 adição	 de	 contatos.	 Ele	 deve	 receber	 um	 objeto	 do	 tipo
	Contato		como	argumento	e	encapsular	totalmente	o	trabalho	com	o	banco	de	dados.	Internamente,	use
um		PreparedStatement		como	vimos	na	aula	para	executar	o	SQL:


		public	void	adiciona(Contato	contato) {
						String	sql	=	"insert	into	contatos	"	+
														"(nome,email,endereco,dataNascimento)"	+
														"	values	(?,?,?,?)";
						try	{
										//	prepared	statement	para	inserção
										PreparedStatement	stmt	=	connection.prepareStatement(sql);
										//	seta	os	valores
										stmt.setString(1,contato.getNome());
										stmt.setString(2,contato.getEmail());
										stmt.setString(3,contato.getEndereco());
										stmt.setDate(4,	new	Date(
																		contato.getDataNascimento().getTimeInMillis()));
										//	executa
										stmt.execute();
										stmt.close();
						}	catch	(SQLException	e)	{
										throw new	RuntimeException(e);
						}
		}
    
    
ATENÇÃO:	Lembre-se	de	importar	as	classes	de	SQL	do	pacote		java.sql	,	inclusive	a	classe
Date!

Antes	de	testar	a	inserção	de	um	contato	no	banco	de	dados,	verifique	se	a	tabela		contatos		existe.
Abra	o	terminal	e	digite:


		mysql	-u	root
    
    
Lembrando	que,	se	houver	senha	para	logar	com	o	banco,	é	preciso	escrever	
mysql	-u	root	-p		

em	seguida,	digitar	a	senha	do	banco	de	dados.
Dentro	do	MySQL,	liste	todas	as	tabelas	já	criadas	com	o	comando:

		show tables;
    
Procure	pela	tabela		contatos	.	Se	ela	não	existir,	crie	com	o	comando:


		create table	contatos	(
				id	BIGINT NOT	NULL	AUTO_INCREMENT,
        nome	VARCHAR(255),
				email	VARCHAR(255),
				endereco	VARCHAR(255),
				dataNascimento	DATE,
				primary key	(id)
		);
		
		
    
(Dica:	esse	código	encontra-se	no	arquivo		contatos.sql		na	pasta		21/projeto-jdbc	)


1.	 Para	testar	nosso	DAO,	desenvolva	uma	classe	de	testes	com	o	método		main	.	Por	exemplo,	uma
chamada		TestaInsere		no	pacote		br.com.caelum.jdbc.teste	.	Gere	o		main		pelo	Eclipse.
O	nosso	programa	de	testes	deve,	dentro	do		main	,	criar	um	novo	objeto		Contato		com	dados	de
teste	e	chamar	a	nova	classe		ContatoDao		para	adicioná-lo	ao	banco	de	dados:



		//	pronto	para	gravar
		Contato	contato	=	new	Contato();
		contato.setNome("Caelum");
		contato.setEmail("contato@caelum.com.br");
		contato.setEndereco("R.	Vergueiro	3185	cj57");
		contato.setDataNascimento(Calendar.getInstance());
		//	grave	nessa	conexão!!!
		ContatoDao	dao	=	new	ContatoDao();
		//	método	elegante
		dao.adiciona(contato);
		System.out.println("Gravado!");
    
    
Execute	seu	programa	e	veja	se	tudo	correu	bem.
1.	 Verifique	se	o	contato	foi	adicionado.	Abra	o	terminal	e	digite:
2.	 

				mysql	-u	root
				use	fj21;
				select	*	from	contatos;
				
        
Lembrando	 que	 se	 houver	 senha	 para	 o	 usuário		root	,	 o	 primeiro	comando	 deve	 ser:		mysql	 -u
root	-p	.	Digite		exit		para	sair	do	console	do	MySQL.
        
 Para	 pesquisar	também	 utilizamos	 a	interface	 	PreparedStatement		 para	 montar	 nosso	 comando
SQL.	 Mas	 como	 uma	 pesquisa	 possui	 um	 retorno	 (diferente	 de	 uma	 simples	 inserção),	 usaremos	 o
método		executeQuery		que	retorna	todos	os	registros	de	uma	determinada	query.
O	objeto	retornado	é	do	tipo		ResultSet		do	JDBC,	o	que	nos	permite	navegar	por	seus	 registros
através	do	método		next	.	Esse	método	retornará		false		quando	chegar	ao	fim	da	pesquisa,	portanto
ele	é	normalmente	utilizado	para	fazer	um	laço	nos	registros:



		//	pega	a	conexão	e	o	Statement     
		
	Connection	con	=	new	ConnectionFactory().getConnection();
		PreparedStatement	stmt	=	con.prepareStatement("select	*	from	contatos");
		//	executa	um	select
		ResultSet	rs	=	stmt.executeQuery();
		//	itera	no	ResultSet
		while	(rs.next())	{
		}
		rs.close();
		stmt.close();
		con.close();
		
		
		
Para	 retornar	 o	 valor	 de	 uma	 coluna	 no	 banco	 de	 dados,	 basta	 chamar	 um	 dos	métodos		get		 do
	ResultSet	,	dentre	os	quais,	o	mais	comum:		getString
	
	
		//	pega	a	conexão	e	o	Statement
		Connection	con	=	new	ConnectionFactory().getConnection();
		PreparedStatement	stmt	=	con.prepareStatement("select	*	from	contatos");
		//	executa	um	select
		ResultSet	rs	=	stmt.executeQuery();
		//	itera	no	ResultSet
		while	(rs.next())	{
				String	nome	=	rs.getString("nome");
				String	email	=	rs.getString("email")
				System.out.println(nome	+	"	_	"	+	email);
		}
		stmt.close();
		con.close();
		
		
RECURSO	AVANÇADO:	O	CURSOR
Assim	 como	 o	 cursor	 do	 banco	 de	 dados,	 só	 é	 possível	mover	 para	 o	 próximo	 registro.	 Para
permitir	um	processo	de	leitura	para	trás	é	necessário	especificar	na	abertura	do		ResultSet		que
tal	cursor	deve	ser	utilizado.
Mas,	 novamente,	 podemos	 aplicar	 as	 ideias	 de	DAO	 e	 criar	 um	 método	 	getLista()		 no	 nosso
	ContatoDao	.	 Mas	 o	 que	 esse	 método	 retornaria?	 Um	 	 ResultSet	 ?	 E	 teríamos	 o	 código	 de
manipulação	de		ResultSet		espalhado	por	todo	o	código?	Vamos	fazer	nosso		getLista()		 devolver
algo	mais	interessante,	uma	lista	de		Contato	:

		PreparedStatement	stmt	=	this.connection
										.prepareStatement("select	*	from	contatos");
		ResultSet	rs	=	stmt.executeQuery();
		List<Contato>	contatos	=	new	ArrayList<Contato>();	
		while	(rs.next())	{
						//	criando	o	objeto	Contato
						Contato	contato	=	new	Contato();
						contato.setNome(rs.getString("nome"));
						contato.setEmail(rs.getString("email"));
						contato.setEndereco(rs.getString("endereco"));
						//	montando	a	data	através	do	Calendar
						Calendar	data	=	Calendar.getInstance();
						data.setTime(rs.getDate("dataNascimento"));
						contato.setDataNascimento(data);
						//	adicionando	o	objeto	à	lista
						contatos.add(contato);
		}
		rs.close();
		stmt.close();
		return	contatos;
		
		
        

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

				mysql	-u	root
				use	fj21;
				select	*	from	contatos;
        
Lembrando	 que	 se	 houver	 senha	 para	 o	 usuário		root	,	 o	 primeiro	comando	 deve	 ser:		mysql	 -u
root	-p	.	Digite		exit		para	sair	do	console	do	MySQL.
        
        
        

---
layout: post
author: Eryx
title: Operações de dados para SQLite em uma aplicação WPF
date: 28/08/2019
---

Este texto contém exemplos de código para operações de dados utilizando o pacote NuGet sqlite-net-pcl.

## Configurações

O pacote NuGet sqlite-net-pcl fornece recursos para armazenamento de dados em SQLite3 para .NET, .NET Core, e Mono. O pacote contém recursos ORM com base em padrões e atributos que possibilitam utilizar a biblioteca sem alterações no modelo de dados da sua aplicação.

Para utilizar os recursos da biblioteca, adicione uma referência ao SQLite.

	using SQLite;

Você precisará definir atributos nas entidades (classes) da aplicação que coincidam com a nomeclatura de tabelas e campos utilizadas na base de dados SQLite. A configuração de atributos é importante para manter a consistência dos padrões de nomeclattura utilizados no backend (Pascal Case, ex.: SimualacaoId) e no banco de dados (Lowercase, ex.: simulacao_id).

O exemplo a seguir ilustra alguns atributos comuns utilizados para decorar a classe Simulacao.

	[Table("simulacoes")]
    public class Simulacao
    {
        public Simulacao()
        {
            Videos = new List<SimulacaoVideo>();
        }

        [Column("id"), PrimaryKey, AutoIncrement]
        public int Id { get; set; }

        [Column("descricao")]
        public string Descricao { get; set; }

        [Ignore]
        public List<SimulacaoVideo> Videos { get; set; }
    }

Veja um resumo dos atributos utilizados no código acima.

* Table - atributo que define o nome da tabela.
* Column - atributo que define o nome desta coluna na tabela.
* PrimaryKey - este atributo faz com que o campo seja definido como chave-primária da tabela.
* AutoIncrement - atributo para auto-incrementar o valor do campo para cada registro.
* Ignore - propriedades de navegação utilizadas para referênciar tabelas relacionadas.
* Indexed - este atributo define que a coluna será indexada para agilizar consultas.
* MaxLength - atributo para definir o tamanho do campo em caracteres (ex.: [MaxLength(50)]).
* Unique - atributo para definir restrição de valores únicos para o campo.

## Consulta dados sem parâmetros

O código abaixo inicia uma conexão com o banco de dados, cria a tabela simulacoes caso não exista, obtém os dados da tabela e preenche na variável simulacoes, e em seguida, atribui o objeto de dados ao grid de simulações.

	private void LoadSimulacoes()
	{
		try
		{
			using (var conn = new SQLite.SQLiteConnection(DatabaseObjects.filePath))
			{
				conn.CreateTable<simulacoes>();
				var simulacoes = conn.Table<simulacoes>().ToList();
				SimulacoesDataGrid.ItemsSource = simulacoes;
			}
		}
		catch (Exception ex)
		{
			throw;
		}
	}

A primeira versão de código (abaixo) utilizava outros componentes NuGet (ex.: SQLite, SQLite.EF6, etc.) que não conseguem obter novos registros do banco de dados SQLite sem que fosse necessário reiniciar a aplicação.

	using (var conn = new SQLiteConnection(DatabaseObjects.ConnectionString))
	{
		using (var cmd = new SQLiteCommand())
		{
			cmd.Connection = conn;
			cmd.CommandType = CommandType.Text;
			cmd.CommandText = DatabaseObjects.SqlSelectSimulacoes;

			conn.Open();
			SQLiteDataReader reader = cmd.ExecuteReader();

			while (reader.Read())
			{
				Simulacao simulacao = new Simulacao { Id = reader.GetInt32(0), Descricao = reader.GetString(1) };
				Simulacoes.Add(simulacao);
				LoadVideos(simulacao);
			}
			SimulacoesDataGrid.ItemsSource = Simulacoes.Count > 0 ? Simulacoes : new List<Simulacao>();
		}
	}

## Consulta dados com parâmetros

Você pode utilizar LINQ para definir parâmetros para consulta e obtenção de dados. Veja o exemplo a seguir.

	var videos = conn.Table<SimulacaoVideo>().Where(o => o.SimulacaoId == simulacao.Id).ToList();

Mais uma vez, compare com o código abaixo e observe que a abordagem acima utiliza menos código para realizar a mesma operação.

	using (var cmd = new SQLiteCommand())
	{
		cmd.Connection = conn;
		cmd.CommandType = CommandType.Text;
		cmd.CommandText = DatabaseObjects.SqlSelectSimulacoesVideo;
		cmd.Parameters.Add(new SQLiteParameter("@param1", DbType.Int32) { Value = simulacao.Id });

		conn.Open();
		SQLiteDataReader reader = cmd.ExecuteReader();
		while (reader.Read())
		{
			SimulacaoVideo video = new SimulacaoVideo { Id = reader.GetInt32(0) };
			simulacao.Videos.Add(video);
		}
	}

## Inserindo registros

Para inserir dados, crie uma instância do objeto a ser inserido e utilize o comando Insert. Veja exemplo a seguir.

	...
	
	var novaSimulacao = new Simulacao { Descricao = DescricaoTextBox.Text };
	using (var conn = new SQLite.SQLiteConnection(DatabaseObjects.filePath))
	{
		conn.CreateTable<Simulacao>();
		conn.Insert(novaSimulacao);
	}
	
	...

Vale reforçar a diferença na quantidade de código necessário para executar a mesma operação de insert quando não estamos utilizando o componente sqlite-net-pcl. 

	...
	
	using (var conn = new SQLiteConnection(DatabaseObjects.ConnectionString))
	{
		using (var cmd = new SQLiteCommand())
		{
			cmd.Connection = conn;
			cmd.CommandType = CommandType.Text;
			cmd.CommandText = DatabaseObjects.SqlInsertSimulacao;
			cmd.Parameters.Add(new SQLiteParameter("@param1", DbType.String) { Value = DescricaoTextBox.Text });
			conn.Open();
			result = (long)cmd.ExecuteScalar();
		}
	}
	
	...

## Resumo

A biblioteca sqlite-net-pcl funciona de maneira semelhante a outros ORMs. Depois de criar uma instância de um objeto novo a ser inserido e enviá-lo como parâmetro para a função Insert() o campo identificador (ex.: Id) será preenchido automaticamente depois que a operação de inclusão for concluída.

Observe que não foi necessário fechar explicitamente o objeto de conexão (ex.: conn.close()) porque utilizamos o comando using que faz o fechamento da conexão automaticamente. 

## Referências

[Repositório GitHub sqlite-net-pcl](https://github.com/praeclarum/sqlite-net)





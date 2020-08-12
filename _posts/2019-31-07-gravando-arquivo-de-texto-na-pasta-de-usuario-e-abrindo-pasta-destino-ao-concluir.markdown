---
layout: post
author: Eryx
title: Arquivo de texto na pasta do usuário do Windows
date: 31/07/2019
---

Este texto mostra o uso do objeto Environment para obter a pasta do perfil de usuário logado no Windows para criação de arquivo de texto. 

A criação de arquivos e pastas deve ser feita apenas no ambiente de usuário do Windows para evitar problemas de permissão de acesso. Por padrão o sistema dispara uma exceção e impede a criação de arquivos e pastas na unidade C:\ por exemplo.

	private void GerarArquivoButton_Click(object sender, RoutedEventArgs e)
	{
		string dir = Environment.GetFolderPath(Environment.SpecialFolder.ApplicationData);
		string path = System.IO.Path.Combine(dir, "nome-do-arquivo.txt");
		
		// Remove o arquivo existente para criar um novo
		if (File.Exists(path))
		{
			File.Delete(path);
		}

		using (StreamWriter sw = File.CreateText(path))
		{
			sw.WriteLine("Texto do arquivo");
			
			// Abrir pasta com o arquivo criado
			Process.Start(dir);
		}
	}

O código acima será executado ao clicar no botão para gerar o arquivo. 

A lógica obtém o diretório do perfil de usuário, concatena com o nome do arquivo, garante que um novo arquivo seja criado sempre que o código for executado, e em seguida utiliza o método File.CreateText do objeto StreamWriter para gerar o arquivo. Em seguida utilize Process.Start() para abrir a respectiva pasta.



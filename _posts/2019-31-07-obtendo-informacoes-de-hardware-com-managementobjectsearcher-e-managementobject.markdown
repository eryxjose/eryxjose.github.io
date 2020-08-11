---
layout: post
author: Eryx
title: Obtendo informações de hardware com ManagementObjectSearcher e ManagementObject
date: 30/07/2019
---

Este texto ilustra o uso dos objetos ManagementObjectSearcher e ManagementObject para obter acesso aos recursos de hardware disponíveis no Windows. 

Você pode obter informações sobre as unidades de disco disponíveis no sistema operacional onde sua aplicação está sendo executada com o código a seguir.

	List<HardInfo> hardList = new List<HardInfo>();
	ManagementObjectSearcher searcher = new ManagementObjectSearcher("SELECT * FROM Win32_DiskDrive");
	foreach (ManagementObject wmi_HD in searcher.Get())
	{
		HardInfo hd = new HardInfo()
		{
			Model = wmi_HD["Model"].ToString(),
			Type = wmi_HD["InterfaceType"].ToString(),
			SerialNo = wmi_HD["SerialNumber"].ToString()
		};
		hardList.Add(hd);
	}
	
A classe HardInfo foi criada para armazenar detalhes dos objetos identificados e pode ser vinculada a um DataGrid utilizando a propriedade ItemsSource.

	UnidadesDataGrid.ItemsSource = hardList;

Veja o código da classe HardInfo a seguir.

	public class HardInfo
    {
        public string Model { get; set; }
        public string Type { get; set; }
        public string SerialNo { get; set; }
    }

De maneira semelhante, o exemplo abaixo ilustra a obtenção de informações sobre os adaptadores de rede disponíveis. Para obter o endereço MAC Address é necessário testar se o adaptador identificado possui a referida propriedade. Veja abaixo.

	ManagementObjectSearcher searcherMac = new ManagementObjectSearcher("SELECT * FROM Win32_NetworkAdapterConfiguration");
	string macAddress = String.Empty;
	foreach (ManagementObject wmi_NET in searcherMac.Get())
	{
		object tempMacAddrObj = wmi_NET["MacAddress"];
		// Verifica se o elemento de iteração possui a propriedade MacAddress.
		if (tempMacAddrObj == null)
		{
			continue;
		}
		// Garante que apenas o Mac Address da primeira placa seja obtido
		if (macAddress == String.Empty)
		{
			macAddress = tempMacAddrObj.ToString();
			HardInfo macAddr = new HardInfo()
			{
				Model = "Network Adapter",
				Type = "MAC ADDRESS",
				SerialNo = macAddress
			};
			// Armazenamos o resultado na classe HardInfo e também incluímos em hardList.
			hardList.Add(macAddr);
		}
	}

Os exemplos de código para leitura de recursos de hardware do sistema ilustrados neste texto, podem ser disponibilizados em um método chamado na inicialização da aplicação. Imagine um método GetDriveInvo() executado em MainWindow() de sua aplicação.

	public MainWindow()
	{
		InitializeComponent();
		GetDriveInfo();
	}

Relevante lembrar que searcherMac.Get() funciona apenas para código executado em nível segurança Full Trust. Consulte as referências para mais informações.


## Referências

* [ManagementObject.Get Method](https://docs.microsoft.com/en-us/dotnet/api/system.management.managementobject.get?view=netframework-4.8)
* [Windows Management Instrumentation](https://docs.microsoft.com/en-us/windows/win32/wmisdk/wmi-start-page)
* [ManagementObject Class](https://docs.microsoft.com/en-us/dotnet/api/system.management.managementobject?view=netframework-4.8)


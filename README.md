# Free-VPS
nome : Windows-RDP

em : workflow_dispatch

empregos :
  construir :

    run-on : windows-latest
    minutos de tempo limite : 999999999999

    passos :
    - nome : Baixar Ngrok.
      executar : |
        Invoke-WebRequest https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-windows-amd64.zip -OutFile ngrok.zip
        Invoke-WebRequest https://raw.githubusercontent.com/DinhPhuc/windows-rdp/main/start.bat -OutFile start.bat
        Invoke-WebRequest https://raw.githubusercontent.com/DinhPhuc/windows-rdp/main/wallpaper.png -OutFile wallpaper.png
        Invoke-WebRequest https://raw.githubusercontent.com/DinhPhuc/windows-rdp/main/wallpaper.bat -OutFile wallpaper.bat
        Invoke-WebRequest https://raw.githubusercontent.com/DinhPhuc/windows-rdp/main/loop.bat -OutFile loop.bat
    - nome : Baixe o Launcher.
      executar : |
        Invoke-WebRequest https://raw.githubusercontent.com/DinhPhuc/windows-rdp/main/launcher/Node.js.lnk -OutFile Node.js.lnk
        Invoke-WebRequest https://raw.githubusercontent.com/DinhPhuc/windows-rdp/main/launcher/Visual%20Studio%202019.lnk -OutFile "Visual Studio 2019.lnk"
        Invoke-WebRequest https://github.com/DinhPhuc/windows-rdp/raw/main/launcher/Ganti%20Password.exe -OutFile "Ganti Password.exe"
    - nome : Extraia o arquivo Ngrok.
      execute : Expand-Archive ngrok.zip
    - nome : Conecte Ngrok.
      execute : .\ngrok\ngrok.exe token de autenticação $Env:NGROK_AUTH_TOKEN
      ambiente :
        NGROK_AUTH_TOKEN : ${{ secrets.NGROK_AUTH_TOKEN }}
    - nome : Ação de Acesso RDP.
      executar : |
        Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server'-name "fDenyTSConnections" -Value 0
        Enable-NetFirewallRule -DisplayGroup "Área de Trabalho Remota"
        Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" -Value 1
        copiar papel de parede.png D:\a\wallpaper.png
        copiar wallpaper.bat D:\a\wallpaper.bat
        copiar Node.js.lnk C:\Users\Public\Desktop\Node.js.lnk
        copie "Visual Studio 2019.lnk" "C:\Users\Public\Desktop\Visual Studio 2019.lnk"
        copie "Ganti Password.exe" "C:\Users\Public\Desktop\Ganti Password.exe"
    - nome : Fazendo túneis
      run : Start-Process Powershell -ArgumentList '-Noexit -Command ".\ngrok\ngrok.exe tcp --region ap 3389"'
    - nome : Conecte RDP CPU 2 Core - 7 GB Ram - 256 SSD.
      execute : cmd /c start.bat
    - nome : Implementação bem sucedida! Você pode fechar a guia agora.
      execute : cmd /c loop.bat

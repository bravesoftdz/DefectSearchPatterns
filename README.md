# DefectSearchPatterns
Padrões que uso para descobrir defeitos em código.

## Código sonda
Um código fazia uma requisição HTTP e parou de funcionar. Alguma coisa de alguma forma afetou o componente de fazer requisições HTTP.
Crio o menor código possível com uma requisição HTTP e o coloco no ponto mais inicial da execução da aplicação.
Se a requisição funciona, vou colocando ela em pontos mais tardios da execução da aplicação, no momento que ele não funciona, significa que o defeito está em um ponto anterior.
Sempre colocando o código no meio dos métodos, então na metade da metade, e assim por diante, se aprofundando nos submétodos até chegar no no defeito.
Descobri que era um código que descarregava a DLL de socket.

  PROCEDURE TESTEIDHTTTP;
  VAR
    lRequest :TStringStream;
    lResponse : TStringStream;
    AIdHTTP: TIdHTTP;
  BEGIN
    AIdHTTP := TIdHTTP.Create(nil);
    lRequest := TStringStream.Create('');
    lResponse := TStringStream.Create('');
    try
      AIdHTTP.Request.CustomHeaders.Clear;
      AIdHTTP.Request.CustomHeaders.Add('Authorization:Basic UEc6Q2JCYkYybjRGbzlD');
      AIdHTTP.Post('http://172.21.4.13:8080/uas/oauth/token?grant_type=password&username=FULANO&password=abc123', lRequest, lResponse);
    finally
      lRequest.Free();
      lResponse.Free();
      AIdHTTP.Free;
    end;
  END;

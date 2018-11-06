# DefectSearchPatterns
Padrões que uso para descobrir defeitos em código.

## Código sonda
Um código fazia uma requisição HTTP e parou de funcionar. Alguma coisa de alguma forma afetou o componente de fazer requisições HTTP.
Crio o menor código possível com uma requisição HTTP e o coloco no ponto mais inicial da execução da aplicação.
Se a requisição funciona, vou colocando ela em pontos mais tardios da execução da aplicação, no momento que ele não funciona, significa que o defeito está em um ponto anterior.
Sempre colocando o código no meio dos métodos, então na metade da metade, e assim por diante, se aprofundando nos submétodos até chegar no defeito.
Descobri que era um código que descarregava a DLL de socket.

```pascal
  procedure TesteIdHTTP;
  var
    lRequest :TStringStream;
    lResponse : TStringStream;
    AIdHTTP: TIdHTTP;
  begin
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
  end;
```

## Exclusão de código suspeito
Um procedimento apresenta lentidão. Códigos suspeitos de causar a lentidão são comentados e se verifica se o procedimento ficou sem a lentidão. Se ficou rápido, um a um os códigos vão sendo descomentados, até ficar lento novamente. O último código descomentado é o causador do problema. Se há submétodos, reinicia-se o processo dentro do método.

## Interrupção de código
Um memory leak acontece após um código. Adiciono um comando que interrompe o método no meio, se ocorreu o memory leak, ele está antes da interrupção, se não está depois. Vai se refinando a posição da interrupção metade a metade, até encontrar o método problemático. Se ele tem submétodos, aprofunda-se neste, repetindo-se a operação.


# Tipos de bugs

1 - Acidentais - Fáceis de resolver
2 - Problema de modelagem - Dificeis, mas são os mais recompensadores de se resolver. Suas soluções amadurecem o código.

https://twitter.com/acdlite/status/1058092028460916736

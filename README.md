# coltapi
API Delphi HTTP Client Request (ColtAPI) - declaração de interfaces para automatizar as requisições HTTP

>> Declaração da Interface com a estrutura do serviço (NO CODE - não requer implementação)

    [host('https://servidor.com.br')]
    TCustomService = interface(INetClientInvoke) // classe padrão
    end;

    [base('/api/v2')]
    TMyService = interface(TCustomService) // herdado da classe padrão

      [meth(GET)]
      [path('/consumer/all')]
      function GetConsumers: TArray<TConsumer>;

      [meth(GET)]
      [path('/consumer/{id}')]
      function FindConsumer(id: Integer): TConsumer;

      [meth(POST)]
      [path('/consumer')]
      function RegisterConsumer(consumerObject: TConsumer): TConsumerStatus;

    end;
  
>> Exemplos de uso:
  
    interface
  
    uses
      colt.net;
  
    type
  
      TConsumerStatus = record
        status : (stError, stInserted, stUpdated, stDeleted);
        id     : Integer;
        name   : String;
      end;
  
      TConsumer = record
        id     : Integer;
        name   : String;
        salary : Double;
        regDate: TDateTime;
        active : Boolean;
      end;
  
    type
  
      [host('https://servidor.com.br')]
      TCustomService = interface(INetClientInvoke) // classe padrão
      end;
  
      [base('/api/v2')]
      TMyService = interface(TCustomService) // herdado da classe padrão
  
        [meth(GET)]
        [path('/consumer/all')]
        function GetConsumers: TArray<TConsumer>;
  
        [meth(GET)]
        [path('/consumer/{id}')]
        function FindConsumer(id: Integer): TConsumer;
  
        [meth(POST)]
        [path('/consumer')]
        function RegisterConsumer(consumerObject: TConsumer): TConsumerStatus;
  
      end;
  
    implementation
  
    uses
      Dialogs;
  
    procedure Teste;
    var
      Service: TMyService;
      List   : TArray<TConsumer>;
      Item   : TConsumer;
      Resp   : TConsumerStatus;
      s : string;
    begin
      NetClient.Get(Service);
      Service.AuthenticateBasic('luiz', '123');
  
      List   := Service.GetConsumers;
      Item   := Service.FindConsumer(2);
  
      Resp := Service.RegisterConsumer(Item);
      if Resp.status = stError then
        ShowMessage('Ocorreu um erro')
      else
      begin
        case Resp.status of
        stError    : s := 'ERRO';
        stInserted : s := 'Inserido';
        stUpdated  : s := 'Atualizado';
        stDeleted  : s := 'Excluido';
        else
          s := '';
        end;
  
        ShowMessage('Processo concluido com sucesso: ' + s);
      end;
    end;
  
    end.
      

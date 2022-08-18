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
   
      // >> Internal Types in NetClient (colt.net.pas) <<
      //   TResponseOK    = type Boolean;
      //   TResponseCode  = type Integer;
      //   TResponseText  = type string;
      //   TResponseData  = type string; // String: Response Data for Status Code (TBytesStream Response)
      //   TResponseResumeLog  = type string; // ReportStatus Log (resumido - sem header)
      //   TResponseReportLog  = type string; // ReportStatus Log (completo - com header e response data)
      //   TResponseComputeLog = type string; // RELEASE: Log Basico (sem header); DEBUG: Log Completo(inclusive header e senhas);
      
      // >> Internal Attributes <<
	  //   input:request (class,methods)
	  //     soap  class: soapAction (WSDL-WebServices with Envelope)
	  //     host  class: host path, include Variables
	  //     base  class: base path, include Variables
	  //     path  methods: path (url: host + base + path), include Variables
	  //     meth  GET, POST, etc

	  //   input:names (methods params)
	  //     name  params: também pode usar no result para indicar um node json especifico
	  //     head  params: header Net send, include Variables
	  //     body  params: body Net send, include Variables

	  //   output:response (returns)
	  //     http  HTTP Status Code
	  //     json  intercept return - string: json name; Integer: json index array    

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

      TComplexConsumer = record
      public
        function StatusSuccess: Boolean; // result := (StatusCode = 201);
      public
       
        StatusCode : NetClient.TResponseCode; // internal type (automatic set value)
        StatusText : NetClient.TResponseText; // internal type (automatic set value)
        Response   : NetClient.TResponse;     // internal type (automatic set value)
        
        [jsCase('StatusSuccess')]     // -> StatusSuccess is function (jsCase in colt.json.pas)
        Data       : TConsumer;       //    StatusSuccess is True e json response contém node "Data" com estrutura de TConsumer
        
        [jsCase('StatusCode', 201)]   // -> StatusCode is field (jsCase in colt.json.pas)
        Consumer   : TConsumer;       //    StatusCode equal 201 e json response contém node "Consumer" com estrutura de TConsumer
        
        [jsCase('StatusCode', 400)]   // -> StatusCode is field (jsCase in colt.json.pas)
        Error      : TErrorMessage;   //    StatusCode equal 400 e json response contém node "Data" com estrutura para TErrorMessage
     
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
  
        [meth(GET)]
        [path('/complex/consumer/{id}')]
        function FindConsumerComplex(id: Integer): TComplexConsumer;

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
  
      List   := Service.GetConsumers;     // -> 1) List All
      Item   := Service.FindConsumer(2);  // -> 2) List id=2
  
      Resp := Service.RegisterConsumer(Item); /// response json = {"status":1,"id":2,"name":"teste"}
      if Resp.status = stError then
        ShowMessage('Ocorreu um erro')
      else
      begin
        case Resp.status of
        stError    : s := 'ERRO';
        stInserted : s := 'Inserido';   // status = 1
        stUpdated  : s := 'Atualizado';
        stDeleted  : s := 'Excluido';
        else
          s := '';
        end;
  
        ShowMessage('Processo concluido com sucesso: ' + s);
      end;
    end;
  
    end.
      


#include <stdio.h>
#include <sys/socket.h>
#include <arpa/inet.h>
#include <stdlib.h>
#include <netdb.h>
#include <string.h>


int createSocket();
char *getIPAddress(char *host);
char *constructHTTPQuery(char *host, char *page);
void pageBrowse(char *,char *);
void linkDisplay();

#define PAGE "/"
#define PORT 80
#define USERAGENT "HTMLGET 1.0"

char content[50][500000];
int pagecount=0;
int count=0;
char f[1000][1000]; //to store the link
FILE *fp;
int flinkcount=0;
int main()
{
	char host[40];
	char page[250];
	
	printf("Enter initial Host\n");
	scanf("%s",host);
	printf("Enter initial Page\n");
	scanf("%s",page);		
	pageBrowse(host,page);
	//printf("%s",content[pagecount]);
	fp=fopen("file.html","w");
	fwrite(content[pagecount],1,sizeof(content[pagecount]),fp);
	fclose(fp);	
	printf("Do you want to generate the links?,Enter 1 for yes\n");
	int linkchoice;	
	scanf("%d",&linkchoice);
	if(linkchoice==1)
	{
		linkDisplay();		
		while(1)
		{	
			pagecount++;
			count=0;
			int choice;	
			printf("Enter the choice of link,-1 to exit\n");
			scanf("%d",&choice);
			if(choice==-1)	
				break;	
			else
			{
				flinkcount=0;
				//printf("%d%s",choice,f[choice]);
				if(f[choice][0]=='/')
				{		
					pageBrowse(host,f[choice]);
				}
				else if(f[choice][0]=='h')
				{
					int count=0;
					int pe=0,he=0;
					char hostextract[40];
					char pageextract[250];
					//printf("--------------------%s--------------------------",f[choice]);
					while(f[choice][count]!=':')
						count++;
					count+=3;
					while(f[choice][count]!='/'&&f[choice][count]!='\0')
					{
						hostextract[he++]=f[choice][count++];					
					}
					hostextract[he]='\0';
					strcpy(host,hostextract);
					if(f[choice][count]=='\0')
						{
							pageextract[0]='/';
							pageextract[1]='\0';
						}
					else
					{						
					while(f[choice][count]!='\0')
					{
						pageextract[pe++]=f[choice][count++];					
					}
					pageextract[pe++]='/';
					pageextract[pe]='\0';
					}
					//printf("%s     %s",hostextract,pageextract);
					pageBrowse(hostextract,pageextract);
				}					
				memset(f,'\0',sizeof(f[0][0]) * 1000 * 1000);
				//printf("%s",content[pagecount]);
				fp=fopen("file.html","w");
				fwrite(content[pagecount],1,sizeof(content[pagecount]),fp);
				fclose(fp);					
				printf("Do you want to generate the links?,Enter 1 for yes\n");	
				int linkchoice1;				
				scanf("%d",&linkchoice1);
				if(linkchoice1==1)
				{					
					linkDisplay();
				}
				else
					break;
			}
		}
	}
}

void linkDisplay()
{
	int i;
	char *c;
        char *temp=content[pagecount];
	
	while(c=strstr(temp,"href"))  //for each iteration new href is found by increasing temp
	{

	if(*(c+4)=='='){
	while(*c!='"')//whether or not space is present
	{
		c+=1;
	}
	c+=1;
	
	int fcout=0;//link length counter variable
	//if(*c=='h'||*c=='/')
	//{	
	while(*c!='"')//until the link ends
	{
		f[flinkcount][fcout++]=*c;
		c++;
	}

	printf("%d ",flinkcount);
	
	f[flinkcount][fcout]='\0';
	while(*c!='>')//for text corresponding to link until >
	{
	c++;	// do nothing
	}
	c++;
	while(*c!='<')//until <
	{
	printf("%c",*c);	// everything is part of text
	c++;
	}
	printf("------>%s\n",f[flinkcount++]);
	temp=(char *)c;//increase temp upto c
	}
	//}
	else
	{
	temp+=4;
	}
	//printf("!");
	}
}


void pageBrowse(char *host,char *page)
{
  	struct sockaddr_in *remote;
  	int sock;
  	int tempvar;
  	char *ip;
  	char *get;
  	char buf[BUFSIZ+1];
 
  	sock = createSocket();
  	ip = getIPAddress(host);
  	fprintf(stderr, "IP is %s\n", ip);
  	remote = (struct sockaddr_in *)malloc(sizeof(struct sockaddr_in *));
  	remote->sin_family = AF_INET;
  	tempvar = inet_pton(AF_INET, ip, (void *)(&(remote->sin_addr.s_addr)));
  	if( tempvar < 0)  
  	{
    		//perror("Can't set remote->sin_addr.s_addr");
    		exit(1);
	}
	else if(tempvar == 0)
  	{
    		fprintf(stderr, "%s is not a valid IP address\n", ip);
   	 	exit(1);
  	}
  	remote->sin_port = htons(PORT);
 
  	if(connect(sock, (struct sockaddr *)remote, sizeof(struct sockaddr)) < 0){
    	perror("Can't connect");
    	exit(1);
  	}

  	get = constructHTTPQuery(host, page);
  	
  	fprintf(stderr, "Query is:\%s\n", get);
 
  	int sent = 0;
  	while(sent < strlen(get))
  	{
    	tempvar = send(sock, get+sent, strlen(get)-sent, 0);
    	if(tempvar == -1){
      	//perror("Can't send query");
      	exit(1);
    	}
    	sent += tempvar;
  	}
  	memset(buf, 0, sizeof(buf));
  	int htmlstart = 0;
  	char *htmlcontent;
  	while((tempvar = recv(sock, buf, BUFSIZ,0)) > 0){
    	if(htmlstart == 0)
    	{
      	htmlcontent = strstr(buf, "\r\n\r\n");
      	if(htmlcontent != NULL){
        htmlstart = 1;
        htmlcontent += 4;
      	}
    	}
	else{
      	htmlcontent = buf;   
	}
    	if(htmlstart){
	int j;	
	for(j=0;j<strlen(htmlcontent);j++)
		content[pagecount][count++]=htmlcontent[j];
    	}
    	memset(buf, 0, tempvar); 
	}
  	if(tempvar < 0)
  	{
    	perror("Error receiving data");
  	}
  	free(get);
  	free(remote);
  	free(ip);
  	close(sock);
} 
 
int createSocket()
{
  	int sock;
  	if((sock = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP)) < 0){
    	perror("Can't create TCP socket");
    	exit(1);
  	}
  	return sock;
}
 
char *getIPAddress(char *host)
{
 	struct hostent *h;
  	int iplen = 15; 
  	char *ip = (char *)malloc(iplen+1);
  	memset(ip, 0, iplen+1);
  
  	if((h = gethostbyname(host)) == NULL)
  	{
    	herror("Can't get IP");
    	exit(1);
  	}
  
  	if(inet_ntop(AF_INET, (void *)h->h_addr_list[0], ip, iplen) == NULL)
  	{
   		perror("Can't resolve host");
    	exit(1);
  	}
  	return ip;
}
 
char *constructHTTPQuery(char *host, char *page)
{
  	char *HTTPreq;
  	char *getpage = page;
  	char *str = "GET /%s HTTP/1.0\r\nHost: %s\r\nUser-Agent: %s\r\n\r\n";
  	if(getpage[0] == '/'){
    	getpage = getpage + 1;
    	fprintf(stderr,"Converting the input into the desired form :  %s to %s\n", page, getpage);
  	}
  	HTTPreq = (char *)malloc(strlen(host)+strlen(getpage)+strlen(USERAGENT)+strlen(str)-5);
  	sprintf(HTTPreq, str, getpage, host, USERAGENT);
  	return HTTPreq;
}


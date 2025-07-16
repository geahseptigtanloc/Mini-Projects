// Geah Sept C. Igtanloc, InTec 1-B
#include <stdio.h>
#include <string.h>
#include <ctype.h>
#define MAX 5
#define MAXSUBJECTS 10
#define CLASS_SIZE 40
#define DATAFILE "A CLASS RECORD"
/*------------------------------------------------------*/
typedef struct {
   char sname[12];
   double grade;
   int units;
   double qpe;
   char letter[3];
} subject;
/*------------------------------------------------------*/
typedef struct {
	char ID[10];
	char firstname[15];
	char lastname[15];
	char course[15];
	subject mysubjects[MAXSUBJECTS];
	double QPI;
	int S_count;
} student;
/*------------------------------------------------------*/
void BubbleLastname(student a[], int l, int S) {
     int i, j, compare;
     student temp;
     for (i = l; i < S - 1; i++)
         for (j = S - 1; j > i; j--) {
             compare = strcmp(a[j-1].lastname, a[j].lastname);
             if (compare > 0) {
                temp = a[j];
                a[j] = a[j-1];
                a[j-1] = temp;
                }
             }
}
/*------------------------------------------------------*/
void Bubblegrade(subject a[], int l, int r)
{
    int i, j;
    subject temp;
    for (i = l; i < r - 1; i++){
        for (j = r - 1; j > i; j--){
            if (a[j].grade > a[j - 1].grade)
            {
                temp = a[j];
                a[j] = a[j - 1];
                a[j - 1] = temp;
            }
        }
    }
} 
/*------------------------------------------------------*/
void BubbleID(student a[], int l, int r)
{ int i, j, compare;
  student temp;
  for (i = l; i < r; i++)
    for (j = r; j > i; j--)  {
       compare = (strcmp(a[j].ID, a[j-1].ID));
       if (compare < 0)  {
	     temp = a[j];
	     a[j] = a[j-1];
	     a[j-1] = temp;  }   }
}
/*------------------------------------------------------*/
int ReadFile(student a[], int b) {
   int STUDENT_count = 0;
   FILE *sfile;
   if ((sfile = fopen(DATAFILE,"rb")) == NULL) {
	printf("Creating new file...");
	getchar(); 
   }
   else {
	fread(a,b,1,sfile);
	STUDENT_count = a[CLASS_SIZE].S_count;
   }
   fclose(sfile);
   return(STUDENT_count);
}
/*------------------------------------------------------*/
void WriteFile(student a[], int S, int b) {
   FILE *sfile;
   a[CLASS_SIZE].S_count = S;
   sfile = fopen(DATAFILE,"wb");
   fwrite(a,b,1,sfile);
   fclose(sfile);
}
/*------------------------------------------------------*/
void AddSubject(student a[], int n, int S) {
   getchar();
   printf("\nProvide The Following Information...");
   printf("\n");
   printf("\nSubject: ");
   scanf("%[^\n]",a[S].mysubjects[n].sname);
   printf("Grade: ");
   scanf("%lf",&a[S].mysubjects[n].grade);
   printf("Units: ");
   scanf("%d",&a[S].mysubjects[n].units);
	
 }
 /*----------------------------------------------------*/
 void ClassList(student a[], int S) {
  int i,count,lastname;
  BubbleLastname(a,0,S);
  printf("\n....................................................................................................\n");
  printf("\t   ID\t\t    Last Name\t\tFirst Name\t\tQPI\t\t   Course");
  printf("\n....................................................................................................\n");
  for (i = 0; i < S; i++) {
      printf("%15s",a[i].ID);
      printf("\t%20s",a[i].lastname);
      printf("%20s",a[i].firstname);
      printf("\t\t%.2lf",a[i].QPI);
     printf("%20s\n",a[i].course);
  }
  printf("\n....................................................................................................\n");
  	printf("Press 'Enter' TWICE After You Are Done Viewing To Continue...");
}
/*----------------------------------------------------*/
int BinarySearch(student a[], char x[], int low, int high) {
  
  while (low <= high) {
    int mid = (low + high) / 2;
	int compare = strcmp (a[mid].ID, x);
    if (compare == 0)
      return mid;

    if (compare < 0)
      low = mid + 1;

    else
      high = mid - 1;
  }

  return -1;
}
/*----------------------------------------------------*/
void Search(student a[], int S) {
  int subjects_count, index, j;
  char ID[10];
  BubbleID (a, 0, S-1);
  printf("\n------------------------------------------------------\n");
  printf("\nSearch ID: ");
  scanf("%s",ID);
  index = BinarySearch(a, ID, 0, S); 
  if (index >= 0) {
    subjects_count = a[index].S_count;
    printf("Subjects:\n");
    for (j = 0; j < subjects_count; j++) 
      printf("[%d]\t%-13s\n",j,a[index].mysubjects[j].sname);
    printf("\n%s's QPI is %.2lf\n",a[index].lastname, a[index].QPI);  
  } // if (index >=0)
  else printf("\nThe entered ID is not a student in the class.\n");
  printf("\n------------------------------------------------------\n");  
	printf("Press 'Enter' TWICE After You Are Done Viewing To Continue...");
}
/*------------------------------------------------------*/
void Letter(student a[],int n, int S) {
	int i=0;
	while (i <= n) {
	    if (a[S].mysubjects[i].grade >= 92.0 && a[S].mysubjects[i].grade <= 100) {
		    strcpy(a[S].mysubjects[i].letter,"A");
                    a[S].mysubjects[i].qpe = 4.0;         
                }
            if (a[S].mysubjects[i].grade >= 88.0 && a[S].mysubjects[i].grade < 92.0) {
		    strcpy(a[S].mysubjects[i].letter,"B+");
                    a[S].mysubjects[i].qpe = 3.5;
                }
                if (a[S].mysubjects[i].grade >= 84.0 && a[S].mysubjects[i].grade <= 87.0) {
		    strcpy(a[S].mysubjects[i].letter,"B");
                    a[S].mysubjects[i].qpe = 3.0;
                }
                if (a[S].mysubjects[i].grade >= 80.0 && a[S].mysubjects[i].grade <= 83.0) {
		    strcpy(a[S].mysubjects[i].letter,"C+");
                    a[S].mysubjects[i].qpe = 2.5;
                }
                if (a[S].mysubjects[i].grade >= 76.0 && a[S].mysubjects[i].grade <= 79.0) {
		    strcpy(a[S].mysubjects[i].letter,"C");
                    a[S].mysubjects[i].qpe = 2.0;
                }
                if (a[S].mysubjects[i].grade >= 72.0 && a[S].mysubjects[i].grade <= 75.0) {
		    strcpy(a[S].mysubjects[i].letter,"D");
                    a[S].mysubjects[i].qpe = 1.0;
                }
                if (a[S].mysubjects[i].grade < 72.0) {
		    strcpy(a[S].mysubjects[i].letter,"F");
                    a[S].mysubjects[i].qpe = 0.0;
                }
        i++;           
	}
}
/*------------------------------------------------------*/ 
double QPI(student a[], int n, int S) {
	int i=0;
	int TU = 0;
	double TQP=0.0;
	while (i <= n) {
	   TQP += (a[S].mysubjects[i].qpe * a[S].mysubjects[i].units);
	   TU += a[S].mysubjects[i].units;
	   i++;    
	}
	a[S].QPI = TQP/TU;
	return a[S].QPI;
}
/*----------------------------------------------------*/
char Menu2(char choice) {
  do {
     getchar();
    printf("____________________________________\n");
     printf("\n<< SUBJECTS >>\n\n");
     printf("[A] Add Subject\n");
     printf("[V] View Subjects\n");
     printf("[B] Back to CLASS Menu \n\n");
     printf("Enter Letter of Choice: ");
     choice = toupper(getchar());
  } while (choice !='A' && choice != 'V' && choice != 'B');
  return choice;
}
/*---------------------------------------------------*/
void ViewSubjects(student a[], int n, int S) {
  int i;
  printf("\n-----------------------------------------------------------------------\n");
  printf("\nThere are %d Subject(s)\n\n",n);
  printf("\n\tSubject\t");
  printf("\t  Grade\t"); 
printf("   QPE\t");
  printf("  Units\t");
  printf("  Letter Grade");
  printf("\n-----------------------------------------------------------------------\n");
  if (n != 0) {
    for (i = 0; i < n; i++) {
      printf("[%d]\t%-13s",i,a[S].mysubjects[i].sname);
      printf("\t%7.2lf",a[S].mysubjects[i].grade);
      printf("\t%7.2lf",a[S].mysubjects[i].qpe);
      printf("\t%5d   ",a[S].mysubjects[i].units);
      printf("\t%-5s\n",a[S].mysubjects[i].letter);
   
    } 
  printf("\nYour QPI is %.2lf\n",a[S].QPI);  
  }
  else {
    printf("                        No Subjects Added                              \n");
  }
}
/*----------------------------------------------------*/
void SubjectsMenu(student a[],int count, int S) {
int quit = 0;
char choice = '\0';
while (!quit) {
     choice = Menu2(choice);
     switch (choice) {
	 case 'A' : 
	           if (count < MAXSUBJECTS) {  
                      AddSubject(a,count,S);
                      Letter(a,count,S);
                      a[S].QPI = QPI(a, count, S);
                      count++;  
	           }
	           else printf("\nMax subjects added"); break;
	 case 'V' :
	          Bubblegrade(a[S].mysubjects, 0, count);
	          ViewSubjects(a,count,S); 
	          break;
	 case 'B' : quit = 1;
     } 
   } 
   a[S].S_count = count;
}
/*----------------------------------------------------*/
void AddStudent(student a[], int S) {
   int count = a[S].S_count;
   printf("\n~DO NOT USE SPACE~");
   printf("\n ID: ");
   scanf("%s",a[S].ID);
   printf(" Last Name: ");
   scanf("%s",a[S].lastname);
   printf(" First Name: ");
   scanf("%s",a[S].firstname);
   printf(" Student's Course: ");
   scanf("%s",a[S].course);
   SubjectsMenu(a,count,S);
}
/*----------------------------------------------------*/
char Menu1(char choice) {
  do {
  	getchar();
     printf("\n____________________________________\n");
     printf("\n<< CLASS >>\n\n");
     printf("[A] Add Student\n");
     printf("[D] Display Class List\n");
     printf("[S] Search Student\n");
     printf("[Q] Quit Program\n\n");
     printf("Enter Letter of Choice: ");
     choice = toupper(getchar());
  } while (choice !='A' && choice != 'S' && choice !='D' && choice != 'Q');
  return choice;
}
/*----------------------------------------------------*/
int StudentMenu(student a[],int S) {
int quit = 0;
char choice = '\0';
while (!quit) {
     choice = Menu1(choice);
     switch (choice) {
	 case 'A' : if (S < CLASS_SIZE) {  
                    AddStudent(a,S);
                    S++;      }
	            else printf("\nCLASS is full\n");
	            break;
	 case 'D' : 
	            ClassList(a,S); 
	 	    getchar(); getchar();
	 	    break;
	 case 'S' : Search(a,S); 
	            getchar(); getchar();
	            break;	   
	 case 'Q' : quit = 1;
     } 
   } 
   return S;
}
/*----------------------------------------------------*/
int main(void) {
   int i, class_count = 0;
   student CLASS1A[CLASS_SIZE+1];
   for (i=0;i<CLASS_SIZE;++i) CLASS1A[i].S_count=0;   
   int class_size = sizeof CLASS1A;
   class_count = ReadFile(CLASS1A,class_size);
   class_count = StudentMenu(CLASS1A,class_count);
   WriteFile(CLASS1A,class_count,class_size);
} // main

# Closor, Super key and Candidate key
---



## Cpp code
```cpp

#include <bits/stdc++.h>
#include<vector>
#include<stdio.h>
#include<string.h>
#include<conio.h>
using namespace std;


//functional dependency representation
struct fd { 
   int left[8];
   int right[8];
   int lcount,rcount;
} f[10];


int attrcount,closcount=0,fdcount;
int closure[10];
char attr[10][25];


void getclosure() {
    int i,j,k,l=0,issubset,found;
    do {
        for(i=0;i<=fdcount;i++) {    //Checking each functional dependancy
            issubset=1;
            for(j=0;j<f[i].lcount;j++) {      // This for loop is for checking if left side of i'th FD is a subser of present closure or not
                found=0;
                for(k=0;k<closcount;k++) {    //checking with each element of closure
                    if(closure[k]==f[i].left[j]) {              // checking if j'th element of i'th FD is present in the closure set
                        found=1;
                        break;
                    }
                }
                if(found==0) {
                    issubset=0;
                    break;
                }
            }
            if(issubset==1) {                          // If left side of i'th FD is a subset
                for(k=0;k<f[i].rcount;k++) {           // This for loop will add all elements in right side of i'th FD if that element is 
                    found=0;                           // not already present in the closure set
                    for(j=0;j<closcount;j++) {
                        if(closure[j]==f[i].right[k])
                            found=1;
                    }
                    if(found==0) {
                        closure[closcount]=f[i].right[k];
                        closcount++;
                    }
                }
            }
        }
        l++;
    } while(l<attrcount);
}



// function to get the position of a attribute in the main attr array
int compare(char temp[25]) {
   int i;
   for(i=0;i<attrcount;i++) {
        if(strcmp(temp,attr[i])==0)
            return i;
    }
    return 0;                                      
}




int main() {

    int i,j,k,attcode;
    char schema[100],temp[45],temp1[50];
    for(i=0;i<10;i++) {                              // maximum number of FD is 10 
        f[i].lcount=0;
        f[i].rcount=0;
    }

    cout<< endl << "Enter the schema: ";
    gets(schema);
    attrcount=0;
    for(i=0;schema[i]!='(';i++);                      // i is now at the starting point of attributes

    do {                                             // this while loop will copy all attributes from the schema to attr array
        j=0;
        i++;
        while(schema[i]!=',' && schema[i]!=')') {     // this while loop is for copying one attribute in temp 
            temp[j]=schema[i];
            j++;
            i++;
        }
        temp[j]='\0';
        strcpy(attr[attrcount],temp);                // keeping attribute from temp to attr
        attrcount++;
    } while(schema[i]==',');


    fdcount=-1;
    cout<< endl << "Enter the functional dependancies..."<< endl << "----------Enter 0 to stop----------" << endl;
    for(i=0;i<10;i++) {
        gets(temp1);                                // Getting FD as input
        if(strcmp(temp1,"0")==0)                    // If input is 0 then break
            break;
        fdcount++;
        j=0;
        if(temp1[0]=='{'||temp1[0]=='(')
            j++;                                   // j is at starting point of FD

        do {                                       // This while loop is for getting all left attributes of the FD of outer for loop            
            if(temp1[j]==',')
                j++;
            k=0;
            while(temp1[j]!=','&&temp1[j]!=')'&&temp1[j]!='}'&&temp1[j]!='-') {           // This while loop will get one attribute in 
                temp[k]=temp1[j];                                                         // left side from the FD of outer for loop
                k++;
                j++;
            }

            temp[k]='\0';
            attcode=compare(temp);                                         // Getting the position of temp in attr array
            f[fdcount].left[f[fdcount].lcount]=attcode;                    // Setting the attribute in appropriate position of our fd structure
            f[fdcount].lcount++;                                           // Incrementing the left count for the fd
        } while(temp1[j]==',');

        if(temp1[j]==')'||temp1[j]=='}')                                   // j will point first character of right side of FD from for loop
            j+=3;                                           
        else if(temp1[j]=='-')
            j+=2;
        if(temp1[j]=='{'||temp1[j]=='(')
            j++;

        do {                                            // This while loop is for getting all right attributes of the FD of outer for loop
            if(temp1[j]==',')
                j++;
            k=0;
            while(temp1[j]!=','&&temp1[j]!=')'&&temp1[j]!='}'&&temp1[j]!='\0') {            // This while loop will get one attribute in                                                 
                temp[k]=temp1[j];                                                           // right side from the FD of outer for loop
                k++;
                j++;
            }
            temp[k]='\0';
            attcode=compare(temp);                                         // Getting the position of temp in attr array
            f[fdcount].right[f[fdcount].rcount]=attcode;                   // Setting the attribute in appropriate position of our fd structure
            f[fdcount].rcount++;                                           // Incrementing theright count for the fd
        } while(temp1[j]==',');

    }


    int setArr[attrcount], minAttr=attrcount;
    for(i=0; i<attrcount; i++) {
        setArr[i] = i;
    }
        
    
    int count = pow(2, attrcount);                        // Total number of subset
    
    cout << endl << endl;
    for (i = 0; i < count; i++) {                         // This for loop is for printing closor of all possible combination of 
        vector<int> subsetArr;                            // attributes and also for getting the number of key attribute in the 
        int p=0;                                          // candidate key
        closcount=0;
        for (int j = 0; j < attrcount; j++) {
            if ((i & (1 << j)) != 0) {
                subsetArr.push_back(j);
                p++;
                closure[closcount]=j;
                closcount++;
            }
        }
        if(closcount != 0) {
            getclosure();
            if(closcount==attrcount && minAttr>p)                      
                minAttr=p;                                      // Getting the min number of attribute for candidate key
            cout<<"Closure of (";
            for(int q=0; q<p-1; q++)
                cout << attr[subsetArr[q]] << ", ";
            cout << attr[subsetArr[p-1]] << ")+  :.............:  ";
            for(j=0;j<closcount;j++) {
                cout << attr[closure[j]] << " ";
            }
            cout << endl;
        }
    }




    cout << endl << endl;
    for (i = 0; i < count; i++) {                       // this for loop is to print all super keys 
        vector<int> subsetArr;                      
        int p=0;
        closcount=0;
        for (int j = 0; j < attrcount; j++) {
            if ((i & (1 << j)) != 0) {
                subsetArr.push_back(j);
                p++;
                closure[closcount]=j;
                closcount++;
            }
        }
        if(closcount != 0) {
            getclosure();
            if(closcount==attrcount) {                                // Print if the current attribute set is a super key with
                cout<<"Super key.....(";                          
                for(int q=0; q<p-1; q++)
                    cout << attr[subsetArr[q]] << ", ";
                cout << attr[subsetArr[p-1]] << ")";
                cout << endl;
            }                  
        }
    }



    
    cout << endl << endl;
    for (i = 0; i < count; i++) {                       // this for loop is to print all candidate keys
        vector<int> subsetArr;
        int p=0;
        closcount=0;
        for (int j = 0; j < attrcount; j++) {
            if ((i & (1 << j)) != 0) {
                subsetArr.push_back(j);
                p++;
                closure[closcount]=j;
                closcount++;
            }
        }
        if(closcount != 0) {
            getclosure();
            if(closcount==attrcount && minAttr==p) {                  // Print if the current attribute set is a super key with
                cout<<"Candidate key.....(";                          // min nummber of attribute(ie.candidate key)
                for(int q=0; q<p-1; q++)
                    cout << attr[subsetArr[q]] << ", ";
                cout << attr[subsetArr[p-1]] << ")";
                cout << endl;
            }                  
        }
    }


    

    return 0;
}

```





## Algorithm for closor
```
        C+ = C;
        while (there is changes to C+)
        {
            do (for each functional dependency X->Y in F)
            {
                if (X⊆C+)
                    then C+= C+∪Y
            }
        }
```


## Sample input
```
Enter the schema: EMPLOYEE(empid,empname,dept,age,salary,experience)

Enter the functional dependancies...
----------Enter 0 to stop----------
empid->empname
{age,experience}->salary
empid->{age,dept}
dept->experience
0
```


## Sample output
```
Closure of (empid)+  :.............:  empid empname age dept experience salary 
Closure of (empname)+  :.............:  empname
Closure of (empid, empname)+  :.............:  empid empname age dept experience salary 
Closure of (dept)+  :.............:  dept experience 
Closure of (empid, dept)+  :.............:  empid dept empname age experience salary 
Closure of (empname, dept)+  :.............:  empname dept experience 
Closure of (empid, empname, dept)+  :.............:  empid empname dept age experience salary 
Closure of (age)+  :.............:  age
Closure of (empid, age)+  :.............:  empid age empname dept experience salary 
Closure of (empname, age)+  :.............:  empname age 
Closure of (empid, empname, age)+  :.............:  empid empname age dept experience salary 
Closure of (dept, age)+  :.............:  dept age experience salary 
Closure of (empid, dept, age)+  :.............:  empid dept age empname experience salary
Closure of (empname, dept, age)+  :.............:  empname dept age experience salary 
Closure of (empid, empname, dept, age)+  :.............:  empid empname dept age experience salary 
Closure of (salary)+  :.............:  salary
Closure of (empid, salary)+  :.............:  empid salary empname age dept experience 
Closure of (empname, salary)+  :.............:  empname salary 
Closure of (empid, empname, salary)+  :.............:  empid empname salary age dept experience
Closure of (dept, salary)+  :.............:  dept salary experience 
Closure of (empid, dept, salary)+  :.............:  empid dept salary empname age experience 
Closure of (empname, dept, salary)+  :.............:  empname dept salary experience
Closure of (empid, empname, dept, salary)+  :.............:  empid empname dept salary age experience 
Closure of (age, salary)+  :.............:  age salary 
Closure of (empid, age, salary)+  :.............:  empid age salary empname dept experience
Closure of (empname, age, salary)+  :.............:  empname age salary
Closure of (empid, empname, age, salary)+  :.............:  empid empname age salary dept experience 
Closure of (dept, age, salary)+  :.............:  dept age salary experience 
Closure of (empid, dept, age, salary)+  :.............:  empid dept age salary empname experience 
Closure of (empname, dept, age, salary)+  :.............:  empname dept age salary experience
Closure of (empid, empname, dept, age, salary)+  :.............:  empid empname dept age salary experience 
Closure of (experience)+  :.............:  experience
Closure of (empid, experience)+  :.............:  empid experience empname age dept salary
Closure of (empname, experience)+  :.............:  empname experience 
Closure of (empid, empname, experience)+  :.............:  empid empname experience age dept salary 
Closure of (dept, experience)+  :.............:  dept experience
Closure of (empid, dept, experience)+  :.............:  empid dept experience empname age salary 
Closure of (empname, dept, experience)+  :.............:  empname dept experience
Closure of (empid, empname, dept, experience)+  :.............:  empid empname dept experience age salary 
Closure of (age, experience)+  :.............:  age experience salary 
Closure of (empid, age, experience)+  :.............:  empid age experience empname salary dept
Closure of (empname, age, experience)+  :.............:  empname age experience salary 
Closure of (empid, empname, age, experience)+  :.............:  empid empname age experience salary dept 
Closure of (dept, age, experience)+  :.............:  dept age experience salary
Closure of (empid, dept, age, experience)+  :.............:  empid dept age experience empname salary
Closure of (empname, dept, age, experience)+  :.............:  empname dept age experience salary 
Closure of (empid, empname, dept, age, experience)+  :.............:  empid empname dept age experience salary 
Closure of (salary, experience)+  :.............:  salary experience 
Closure of (empid, salary, experience)+  :.............:  empid salary experience empname age dept
Closure of (empname, salary, experience)+  :.............:  empname salary experience 
Closure of (empid, empname, salary, experience)+  :.............:  empid empname salary experience age dept 
Closure of (dept, salary, experience)+  :.............:  dept salary experience
Closure of (empid, dept, salary, experience)+  :.............:  empid dept salary experience empname age 
Closure of (empname, dept, salary, experience)+  :.............:  empname dept salary experience 
Closure of (empid, empname, dept, salary, experience)+  :.............:  empid empname dept salary experience age 
Closure of (age, salary, experience)+  :.............:  age salary experience 
Closure of (empid, age, salary, experience)+  :.............:  empid age salary experience empname dept
Closure of (empname, age, salary, experience)+  :.............:  empname age salary experience 
Closure of (empid, empname, age, salary, experience)+  :.............:  empid empname age salary experience dept 
Closure of (dept, age, salary, experience)+  :.............:  dept age salary experience 
Closure of (empid, dept, age, salary, experience)+  :.............:  empid dept age salary experience empname 
Closure of (empname, dept, age, salary, experience)+  :.............:  empname dept age salary experience 
Closure of (empid, empname, dept, age, salary, experience)+  :.............:  empid empname dept age salary experience 


Super key.....(empid)
Super key.....(empid, empname)
Super key.....(empid, dept)
Super key.....(empid, empname, dept)
Super key.....(empid, age)
Super key.....(empid, empname, age)
Super key.....(empid, dept, age)
Super key.....(empid, empname, dept, age)
Super key.....(empid, salary)
Super key.....(empid, empname, salary)
Super key.....(empid, dept, salary)
Super key.....(empid, empname, dept, salary)
Super key.....(empid, age, salary)
Super key.....(empid, empname, age, salary)
Super key.....(empid, dept, age, salary)
Super key.....(empid, empname, dept, age, salary)
Super key.....(empid, experience)
Super key.....(empid, empname, experience)
Super key.....(empid, dept, experience)
Super key.....(empid, empname, dept, experience)
Super key.....(empid, age, experience)
Super key.....(empid, empname, age, experience)
Super key.....(empid, dept, age, experience)
Super key.....(empid, empname, dept, age, experience)
Super key.....(empid, salary, experience)
Super key.....(empid, empname, salary, experience)
Super key.....(empid, dept, salary, experience)
Super key.....(empid, empname, dept, salary, experience)
Super key.....(empid, age, salary, experience)
Super key.....(empid, empname, age, salary, experience)
Super key.....(empid, dept, age, salary, experience)
Super key.....(empid, empname, dept, age, salary, experience)


Candidate key.....(empid)
```

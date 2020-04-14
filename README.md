#include <iostream>
#include <cstdlib>
#include <string>
#include <iomanip>
#include <stdexcept>
using namespace std;//global scope


int  maxrec = 3;//number of nodes


struct Teletype
{
	string name;
	string phoneno;
	Teletype* nextaddr;
};

void populate(Teletype*);//prototype
void display(Teletype*);
void remove(Teletype**, int);//to be able to change wear the head of struct points we use pointer to pointer
void menu();
int search(Teletype*, string);
void insert(Teletype**, int);//to be able to change wear the head of struct points we use pointer to pointer
void sort(Teletype**);
void modify(Teletype**, int);
string tolowerr(string);
void check(Teletype*);

int main()
{
	int i;//long variable
	int position, option=1;
	Teletype* list, * current, * first, *start, *begin;
	string name;
	list = new Teletype; 
	current = list;
	int flag = 0;
	while (option != 7) {
		menu();
		cout << "Command: ";
		cin >> option;
		cin.ignore();
		switch (option)
		{
		case 1:
			//populate the current structure
			for (i = 0; i < maxrec - 1; i++)
			{
				current->nextaddr = new (nothrow) Teletype;//insert new memory for next structure
				populate(current);
				try
				{
					check(current);//check valid address
				}
				catch (invalid_argument & e)
				{
					cout << "BAD_ALLOCATION: " << e.what() << endl;
				}
				current = current->nextaddr;//link
			}
			populate(current);
			current->nextaddr = NULL;
			cout << "\nThe list consists of the following records: \n";
			display(list);
			flag = 1;
			break;
		case 2:
			if (flag == 1) {
				cout << "Enter position of element: ";
				cin >> position;
				cin.ignore();
				try//validate input
				{
					insert(&list, position - 1);
				}
				catch (const invalid_argument & e)
				{
					cout << e.what() << "\n";
					break;
				}
				display(list);//display records
				maxrec++;
			}
			else
				cout << "Create list first!\n";
			break;
		case 3://modify
			if (flag == 1) {
				begin = list;
				cout << "\nWhich element do you want to modify?\n";
				cin >> position;
				cin.ignore();
				//validate input
				try
				{
					modify(&begin, position - 1);
				}
				catch (const invalid_argument & e)
				{
					cout << e.what() << "\n";
					break;
				}
				cout << "\nThe list consists of the following records: \n";
				display(list);//display records
			}
			else
				cout << "Create List first!\n";
			break;
		case 4:// delete node
			if (flag == 1) {
			first = list;
			cout << "\nWhere is the element located?\n";
			cin >> position;
			//validate input
			try
			{
				remove(&list, position - 1);
			}
			catch (const invalid_argument & e)
			{
				cout << e.what() << "\n";
				break;
			}
			//display records
			cout << "\nThe list with deleted record #"<<position<<" : \n";
			display(list);
			maxrec--;
			}
			else
				cout << "Create List first!\n";
			break;
		case 5://search 
			if (flag == 1) {
				start = list;
				cout << "Enter a name (first,last): ";
				getline(cin, name);
				name=tolowerr(name);
				if (!search(start, name))
				{
				cout << "The name is not in the current phone directory!\n";
				}
			}
			else
				cout << "Create List first!\n";
			break;
		case 6://display order
			if (flag == 1) {
				first = list;
				sort(&first);
				cout << "\nThe records sorted: \n";
				display(list);
			}
			else
				cout << "Create List first!\n";
			break;
		case 7://exit
			cout<<"Leaving Program\nBye...\n\n";
			exit(0);
			break;
		default://if case does not exist
			cout << "OPTION INVALID\n";
		}
	}
	system("pause");
	return 0;
}

string tolowerr(string str)
{
	for (int i = 0;i < str.length();i++)
		str[i] = tolower(str[i]);
	return str;
}

void check(Teletype* checked)
{
	if (checked->nextaddr == nullptr)
	{
		throw invalid_argument("Not enough Memory");
	}
	
	return;
}

//implement menu

void menu()
{
	cout << "_______________________________________________________\n";
	cout << "\t\t\tMENU                                              \n";
	cout << "_______________________________________________________\n";
	cout << "Select from Menu\n";
	cout << "\n1. Create a linked list\n";
	cout << "2. Insert a new structure in the linked list\n";
	cout << "3. Modify an existing structure in the linked list\n";
	cout << "4. Delete an existing structure from the list\n";
	cout << "5. Search a structure in the linked list\n";
	cout << "6. Display a report for the user in sorted order\n";
	cout << "7. Exit from the program\n";
	cout << "_______________________________________________________\n";
	return;
}
//implementing the functions

void populate(Teletype* record)
{
	string name;
	cout << "Enter a name: \n";
	getline(cin, name);
	record->name = tolowerr(name);
	cout << "Enter phone number: \n";
	getline(cin, record->phoneno);
	

	return;
}
//implementing the function
void display(Teletype* contents)
{
	cout << setw(30) << "FULL NAME" << setw(20) << "PHONE NUMBER" << endl;
	while (contents != NULL)
	{
		cout << setw(30) << contents->name << setw(20) << contents->phoneno << endl;
		contents = contents->nextaddr;
	}
	cout << endl;
	return;
}
//implementing remove
void remove(Teletype** first, int position)
{
	if (position >= maxrec || position < 0)
	{
		throw invalid_argument("Position is invalid!");
	}
	else
	{
		Teletype* temp = *first;
		Teletype* prev;
		if (position == 0)
		{
			*first = temp->nextaddr;//head points to second term
			free(temp);//free the memory
			return;
		}
		for (int i = 1; i <= position && *first != NULL;i++) {
			prev = temp;//n term
			temp = temp->nextaddr;//(n+1) term
			if (i == position) {
				prev->nextaddr = temp->nextaddr;
				free(temp);//free memory
			}
		}
	}
	return;
}

//implementing insert
void insert(Teletype** first, int position)
{
	if(position>maxrec||position<0)
	{
		throw invalid_argument("Position is invalid!");
	}
	else {
		Teletype* temp = new Teletype;//allocate memory
		Teletype* temp1 = *first;//will move until position reached
		populate(temp);//insert data
		temp->nextaddr;//tail
		if (position == 0)
		{
			temp->nextaddr = *first;//makes new node point to first term
			*first = temp;//head will point to new node
		}
		else {
			for (int i = 0; i < position - 1; i++)
			{
				temp1 = temp1->nextaddr;
			}
			temp->nextaddr = temp1->nextaddr;
			temp1->nextaddr = temp;
		}
	}
	return;
}

//implement search
int search(Teletype* contents, string name)
{
	//flag variable
	int found = 0;
	while (contents != NULL)//moves until the end of list is reached
	{
		if (contents->name == name)
		{
			found = 1;
			cout << '\n' << setw(20) << contents->name << setw(20) << contents->phoneno;
			break;
		}
		else
		{
			contents = contents->nextaddr;
		}
	}
	cout << endl;
	return found;
}

void sort(Teletype** first)
{
	//use 2 for loops to move in linked list check using bubble method take first 2 compare continue comparing until we have compared all
	for (Teletype* start = *first;start != NULL; start=start->nextaddr) {
		for (Teletype* select= start->nextaddr;select != NULL; select=select->nextaddr) {
			if (start->name > select->name) {
				//sort
				string temp = start->name;
				start->name = select->name;
				select->name = temp;
				string temp1 = start->phoneno;
				start->phoneno = select->phoneno;
				select->phoneno = temp1;
				
			}
		}
	}
return;
}

void modify(Teletype** change,int position) {
	int option;
	string name;
	if (position >= maxrec || position < 0)
	{
		throw invalid_argument("Position is invalid!");
	}
	else
	{
		Teletype* temp1 = *change;//assign first node
		for (int i = 0; i < position; i++)//move until we reach node we want to modify
		{
			temp1 = temp1->nextaddr;
		}
		//change name & phone number
		cout << "Select option to modify: \n1. Name\n2. Phone Number\n3. Both\nEnter option: ";
		cin >> option;
		cin.ignore();
		switch (option) {
		case 1:
			cout << "Enter a name: \n";
			getline(cin, name);
			temp1->name = tolowerr(name);
			break;
		case 2:
			cout << "Enter phone number: \n";
			getline(cin, temp1->phoneno);
			break;
		case 3:
			cout << "Enter a name: \n";
			getline(cin, name);
			temp1->name = tolowerr(name);
			cout << "Enter phone number: \n";
			getline(cin, temp1->phoneno);
			break;
		default:
			cout << "INVALID OPTION!";
		}
	}
	return;
}

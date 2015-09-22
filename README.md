# Game-Of-Life
#include <iostream>
#include <fstream>
#include <iomanip>
using namespace std;

//declare and initialze global constant
const int MAX_ARRAY_SIZE = 40;

//Function prototypes
void  PrintGen(char lifeBoard[][MAX_ARRAY_SIZE], ostream& outStream, int numRowsInBoard,
	int numColsInBoard, int generationNum);
void NextGen(char lifeBoard[][MAX_ARRAY_SIZE], int numRowsInBoard, int numColsInBoard);

int main()
{

	//Author: Layomi Dele-Dare, Thamer Matar, Ahmad Matar.
	//This program is used to play "The Game of Life" by John H. Conway
	//The presence of X's represents an organism
	//The presence of .s represents no organism

	//declare and initialize constants
	const int minNumRows = 0;
	const int maxNumRows = 40;
	const int minNumCol = 0;
	const int maxNumCol = 40;
	const int minNumGenerations = 0;
	const char organism = 'X';
	const char noOrganism = '.';

	//declare and initalize variables
	char myLifeBoard[MAX_ARRAY_SIZE][MAX_ARRAY_SIZE] = { '\0' };
	char inputName[80] = { '\0' };
	char outputName[80] = { '\0' };
	int counter;
	int numRows = 0;
	int numCol = 0;
	int rows = 0;
	int col = 0;
	int rowN = 0;
	int colN = 0;
	int numberGenerations = 0;
	int generations = 0;
	ifstream myInputStream;
	ofstream myOutputStream;

	//request filename
	//open the filename and check if it was opened correctly
	cout << "Enter the name of the input file: ";
	cin >> inputName;
	myInputStream.open(inputName);

	//if the file is not opened correctly print an error message
	if (myInputStream.fail())
	{
		cerr << "ERROR: input file not opened correctly" << endl;
		return 1;
	}

	//request file output name
	//check if the file is open correctly
	//we can only check this if the ouput file is a "read only file" or locked

	cout << "Enter the name of the output file: ";
	cin >> outputName;
	myOutputStream.open(outputName);

	//if the file fails to open print an error message
	if (myOutputStream.fail())
	{
		cerr << "ERROR: output file not opened correctly" << endl;
		return 2;
	}

	//if the number of rows is not read
	if (!(myInputStream >> numRows))
	{
		cerr << "ERROR: Cannot read number of rows" << endl;
		return 3;
	}

	//if the number of rows is less than 0 or greater than 40
	if (numRows < minNumRows || numRows > maxNumRows)
	{
		cerr << "ERROR: Read an illegal number of rows for the board" << endl;
		return 4;
	}
	//if the number of columns is not read
	if (!(myInputStream >> numCol))
	{
		cerr << "ERROR: Cannot read number of columns" << endl;
		return 5;
	}
	//if the number of columns is less than 0 or greater than 40
	if (numCol < minNumCol || numCol > maxNumCol)
	{
		cerr << "ERROR: Read an illegal number of columns for the board" << endl;
		return 6;
	}
	//if we the number of generations is not read
	if (!(myInputStream >> numberGenerations))
	{
		cerr << "ERROR: Cannot read the number of generations" << endl;
		return 7;
		
	}
	//if the number of generation is less than 0
	if (numberGenerations < minNumGenerations)
	{
		cerr << "ERROR: Read an illegal number of generations" << endl;
		return 8;
	}

	rowN = numRows - 1;
	colN = numCol - 1;

	//read the first game board from the input
	for (rows = 0; rows < numRows; rows++)
	{
		for (col = 0; col < numCol; col++)
		{
			if(!(myInputStream >> myLifeBoard[rows][col]))
			{
				cerr << "ERROR: Not enough data in the input file" << endl;
				return 9;
			}
			if ((myLifeBoard[rows][col] != noOrganism) && (myLifeBoard[rows][col] != organism))
			{
				cerr << "ERROR: Input data for initial board is incorrect" << endl;
				cerr << "location (" << rows << "," << col << ") is not valid";
				return 10;
			}
		}
	}
	//close my input stream
	myInputStream.close();
	//check and make sure there are no organisms included
	//in the boundaries of the inputfile, then print to the screen
	counter = 0;
	for (rows = 0; rows < numRows; rows++)
	{
		if (myLifeBoard[rows][0] == organism)
		{
			counter++;
		}
		if (myLifeBoard[rows][colN] == organism)
		{
			counter++;
		}
	}
	for (col = 0; col < numCol; col++)
	{
		if (myLifeBoard[rowN][col] == organism)
		{
			counter++;
		}
		if (myLifeBoard[0][col] == organism)
		{
			counter++;
		}
	}
	if (counter > 0)
	{
		cerr << "ERROR: organisms are present in the border of the board" << endl;
		cerr << " please correct your input file" << endl;
		return 11;

	}
	//use the function PrintGen to print to the screen
	PrintGen(myLifeBoard, cout, rows, col, 0);
	//use the function PrintGen to print to the output file
	PrintGen(myLifeBoard, myOutputStream, rows, col, 0);

	//find the indicated number of generations corresponding to the input and print to the screen
	for (generations = 1; generations <= numberGenerations; generations++)
	{
		NextGen(myLifeBoard, numRows, numCol);
		PrintGen(myLifeBoard, myOutputStream, numRows, numCol, generations);
		if (generations == 1 || generations == numberGenerations)
		{
			PrintGen(myLifeBoard, cout, numRows, numCol, generations);
		}
	}
	myOutputStream.close();

	return 0;
}

void  PrintGen(char lifeBoard[][MAX_ARRAY_SIZE], ostream& outStream, int numRowsInBoard,
	int numColsInBoard, int generationNum)
{
	int index1 = 0;
	int index2 = 0;

	//print the name of the gameboard
	if (generationNum == 0)
	{
		outStream << "LIFE initial game board";
		outStream << endl << endl;
	}
	//print the name and indicate what generation it is
	else
	{
		outStream << "LIFE gameboard: generation " << generationNum;
		outStream << endl;
	}

	for (index1 = 0; index1 < numRowsInBoard; index1++)
	{
		for (index2 = 0; index2 < numColsInBoard; index2++)
		{
			outStream << lifeBoard[index1][index2] << " ";
		}
		outStream << endl;
	}
	outStream << endl << endl << endl;
}


	

void NextGen(char lifeBoard[MAX_ARRAY_SIZE][MAX_ARRAY_SIZE], int numRowsInBoard, int numColsInBoard)
{

	char nextGenBoard[MAX_ARRAY_SIZE][MAX_ARRAY_SIZE];
	const char organism = 'X';
	const char noOrganism = '.';
	int rowA = 1;
	int colA = 1;
	int numRowsInBoard2 = 0;
	int numColsInBoard2 = 0;
	int correct = 0;

	
	
	numRowsInBoard2 = numRowsInBoard - 2;
	numColsInBoard2 = numColsInBoard - 2;

	//determines the locations of organisms for the next generation and places those organisms into the array nextGenBoard
	for (rowA = 1; rowA <= numRowsInBoard2; rowA++)
	{
		for (colA = 1; colA <= numColsInBoard2; colA++)
		{
			correct = 0;

			if (lifeBoard[rowA - 1][colA - 1] == organism)
			{
				correct++;
			}
			if (lifeBoard[rowA][colA - 1] == organism)
			{
				correct++;
			}
			if (lifeBoard[rowA + 1][colA - 1] == organism)
			{
				correct++;
			}
			if (lifeBoard[rowA - 1][colA] == organism)
			{
				correct++;
			} if (lifeBoard[rowA + 1][colA] == organism)
			{
				correct++;
			}
			if (lifeBoard[rowA - 1][colA + 1] == organism)
			{
				correct++;
			}
			if (lifeBoard[rowA][colA + 1] == organism)
			{
				correct++;
			}
			if (lifeBoard[rowA + 1][colA + 1] == organism)
			{
				correct++;
			}
			if (correct == 3)
			{
				nextGenBoard[rowA][colA] = organism;
			}
			else if (correct == 2)
			{
				nextGenBoard[rowA][colA] = lifeBoard[rowA][colA];
			}
			else
			{
				nextGenBoard[rowA][colA] = noOrganism;
			}
	
		}
	}
	//Edge elements can never contain organisms, they need not be updated
	for (rowA = 1; rowA <= numRowsInBoard2; rowA++)
	{
		for (colA = 1; colA <= numColsInBoard2; colA++)
		{
			lifeBoard[rowA][colA] = nextGenBoard[rowA][colA];
		}
	}

}

[Home](https://github.com/caelenwalker/1000FinalProject) | [Education and Experience](https://github.com/caelenwalker/1000FinalProject/blob/master/experience.md)
| [Code](https://github.com/caelenwalker/1000FinalProject/blob/master/project.md) | [Activities and Hobbies](https://github.com/caelenwalker/1000FinalProject/blob/master/hobbies.md) | [Contact info](https://github.com/caelenwalker/1000FinalProject/blob/master/info.md)

# IT 2040 Document Merger

## This project merges two documents into one. You can see how my test and test1 documents become merged.

Program.cs:

```C#
using System;
using System.IO;

namespace Document_Merger1 {
    class Program {
        static void Main(string[] args) {
            start:
            //the strings that will be used throughout the program
            string document1 = "";
            string document2 = "";
            string userinput = "";

            Console.WriteLine("*** Document Merger ***\n");

            //prompts the user for the first document name
            //adds .txt to the ends so the file can be found
            Console.Write("Enter the first document name: ");
            document1 = DocumentName();

            do { //will continue looping if the file doesn't exist
                if(File.Exists(document1)) { //if the file exists, continue to run the program
                        break;
                }
                else { //asking the user to re enter the file name
                    Console.WriteLine("The file does not exist. Re enter the file name: ");
                    document1 = DocumentName();
                }
            }while(File.Exists(document1) == false);

            StreamReader sr = new StreamReader(document1); 
            string line = sr.ReadLine(); //reads the line of text from the first file

            //same procedure for the second document
            Console.Write("Enter the second document name: ");
            document2 = DocumentName(); 

            do { 
                if(File.Exists(document2)) {
                    break;
                }
                else {
                    Console.WriteLine("The file does not exist. Re enter the file name: ");
                    document2 = DocumentName();
                }
            }while(File.Exists(document2) == false);
        
            StreamReader sr1 = new StreamReader(document2);
            string line1 = sr1.ReadLine(); //reads the line of text from the second file

            //takes the name of the first and second document and removes the .txt extension
            //makes it into the merged document name
            string merged = document1.Substring(0, document1.Length - 4) + document2.Substring(0, document2.Length - 4) + ".txt";
            string mergedContent = line + line1; //takes the line of text from both files
            StreamWriter sw = new StreamWriter(merged);
            sw.WriteLine(mergedContent); //puts the combined content into the new file
            sw.Close(); //closes the file, basically saves it

            //counts the number of characters in the merged file
            int count = mergedContent.Length;
            Console.WriteLine("{0} was successfully saved. The document contains {1} characters.", merged, count);
        
            //adds .txt to the document name
            static string DocumentName() {
                string document = "";
                document = (Console.ReadLine() + ".txt");
                return document;
            }

            //asks the user if they'd like to merge more files
            do {
                Console.Write("Would you like to do another? (y/n): ");
                Console.ReadLine();
                if(userinput == "y") {
                    goto start;
                }
                else {
                    break;
                }
            }while(true);
        }
    }
}
```

test.txt:
**henry and**

test1.txt:
**caelen are buddies.**

testtest1.txt:
**henry and caelen are buddies.**

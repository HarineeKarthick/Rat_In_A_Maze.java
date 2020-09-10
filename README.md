
import java.util.*;
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
class Rat_In_A_Maze extends Thread implements ActionListener	
{	
	Scanner input=new Scanner(System.in); //creating an object for Scanner class
	int SIZE=8;     //To indicate the size of 2D array. Here I have kept the size of array as 8x8
	int SROW,SCOL;  //SROW and SCOL are the variables to store user input for starting row and starting col index
	int EROW,ECOL;  //EROW and ECOL are the variables to store user input for destination row and destination col index
	boolean returnvalue;
	int i,j,row,col,k=0,x;
	int position;  //variable to keep the equivalent button number for the given row and column
	public int[][] solution=new int[8][8];
	public int[][] maze = new int[SIZE][SIZE];	//Input matrix with walls(indicated by 1) and space(indicated by 0)
	//if there is path the rat keeps moving and display 'blue' color with sleep
	//while backtracking the color 'red' will be displayed with sleep
	int[]color = new int[500]; //To differentiate the backtrack color and the path color.If '1'means it will print blue and '2'means it will print red 
	int[]pos = new int[500];  //To collect the position(button number in a maze) of rat wherever it is going
	JFrame jframe;//creating an object for JFrame
	JButton b[];//creating array of objects for JButton
	JMenuBar mb;
	JMenu m1;
	JMenuItem item1;
	public void userinput()//method to get user inputs
	{
	System.out.println("USER,Please read the below lines carefully!\n\n\n");
	System.out.println("YELLOW BLOCKS represents "+"MAZE at the begining\n\n");
	System.out.println("BLACK BLOCKS represents "+"BLOCKS\n\n");
	System.out.println("BLUE BLOCKS represents "+"PATH TRACED BY RAT\n\n");
	System.out.println("Total ROWS in a grid : 8\nTotal COLUMN in a grid : 8\nYour maximum limit for row and column is 7\n\n");
	System.out.println("Enter the start row:");
	SROW=input.nextInt();
	System.out.println("Enter the start column:");
	SCOL=input.nextInt();
	System.out.println("Enter the destination row:");
	EROW=input.nextInt();
	System.out.println("Enter the destination column:");
	ECOL=input.nextInt();
	}

	boolean solveMaze(int r,int c,int desrow,int descol) //method to find the path for Rat
    	{
		if((r==desrow) && (c==descol))	//If the current row and col are equal to the destination row and col
        	{	
			position = (r * 8) + c;	//calculating the button position for the current row and col
			pos[k]=position;//To store the current position of rat in the maze
			color[k]=1;//To indicate 'blue' color
            		return true;
        	}
        	if(r>=0 && c>=0 && r<SIZE && c<SIZE && solution[r][c] == 0 && maze[r][c] == 0)
        	{
            		//if safe to visit then visit the cell
			position=r*8+c;pos[k]=position;color[k]=1;k++;
			solution[r][c]=1;
			System.out.print("\n\n\n\n");
            		//going down
            		if(solveMaze(r+1, c,desrow,descol))
                	return true;
			//going southeast
            		if(solveMaze(r+1, c+1,desrow,descol))            	
			return true;
            		//going right
            		if(solveMaze(r, c+1,desrow,descol))
			return true;
			//going northeast
            		if(solveMaze(r-1, c+1,desrow,descol))
                	return true;
            		//going up
            		if(solveMaze(r-1, c,desrow,descol))           	
			return true;
            		//going north west
            		if(solveMaze(r-1, c-1,desrow,descol))
                	return true;
			//going left
            		if(solveMaze(r, c-1,desrow,descol))
                	return true;
			//going southwest
            		if(solveMaze(r+1, c-1,desrow,descol))
                	return true;
            		//backtracking
			position=r*8+c;
			pos[k]=position;
			color[k]=2;//To indicate the color is 'RED' because of backtracking
			k++;//Incrementing the value of k
            		return false;
        }
        return false;
    }
		public Rat_In_A_Maze()  //constructor for initialization
	{
		userinput(); //calling method to get user input
		jframe=new JFrame();
		jframe.setTitle("Rat In A Maze"); //setting title for the JFrame
		jframe.setSize(350,350);  //setting size 
		jframe.setDefaultCloseOperation(jframe.EXIT_ON_CLOSE);
		jframe.setLayout(new GridLayout(8,8));  //grid layout is necessary to get 2D array view
 		b=new JButton[64];  //There are 8 rows and 8 cols in our program.so the total no of JButtons are (8x8)=64
		mb=new JMenuBar();
		m1=new JMenu("Menu");
		item1=new JMenuItem("START");
		m1.add(item1);
		mb.add(m1);
		m1.addActionListener(this);//adding actionlistener to the components
		item1.addActionListener(this);
		jframe.setJMenuBar(mb);
		for(int i=0;i<64;i++) //for loop for adding all the JButtons to JFrame
		{
			b[i]=new JButton();
			jframe.add(b[i]);
			b[i].addActionListener(this);
		}
		jframe.setVisible(true); //must be mentioned to make the frame visible
		for(int i=0;i<64;i++)    //Iam keeping all the buttons with 'yellow' color initially
		{
			b[i].setBackground(Color.YELLOW);
		}
			position = (EROW* 8) + ECOL; //calculating the equivalent button position for the destination row and col index
			b[position].setIcon(new ImageIcon("E.jpg")); //setting icon in the destination button
			position = (SROW* 8) + SCOL; //calculating the equivalent button position for the startin row and col index
			b[position].setIcon(new ImageIcon("r.jpg")); //setting icon in the starting button
	}
	public void actionPerformed(ActionEvent ae)
	{
		if(ae.getSource() == item1)
		{
			b[position].setIcon(null);
			b[position].revalidate();
			returnvalue=solveMaze(SROW,SCOL,EROW,ECOL);
			Thread t1=new Thread(new Runnable()
				{
					public void run()
					{
			 		display1();	//call this method to print the path
					}
				});t1.start();
			if(returnvalue==false)	//If there is no path
				System.out.println("No solution\n");
		}
			for(int i=0;i<64;i++)
			{
				if(ae.getSource()==b[i])
				{
					b[i].setBackground(Color.BLACK);
					row=i/8;	//calculating the row index for the current button position
					col=i%8;	//calculating the col index for the current button position
					maze[row][col] = 1;	//storing '1' in the corresponding maze array where the button is clicked in the maze
				}
			}
	}
	public void display1()	//method for printing the path traced by rat with sleep
	{
		for(int v=0;v<pos.length;v++)
		{
			x=pos[v];
			if(color[v]==1)
			{b[x].setBackground(Color.BLUE);try{Thread.sleep(1000);}catch(Exception e){}}
			else{if(color[v]==2){b[x].setBackground(Color.RED);try{Thread.sleep(1000);}catch(Exception e){}}}
		}

	}
	public static void main(String args[])
	{
		
		Rat_In_A_Maze rat=new Rat_In_A_Maze();	//creating object for class 'Rat_In_A_Maze'
	}
}

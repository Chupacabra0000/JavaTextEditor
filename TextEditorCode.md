import javax.swing.*;
import javax.swing.event.ChangeEvent;
import javax.swing.event.ChangeListener;
import javax.swing.filechooser.FileNameExtensionFilter;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.io.File;
import java.io.FileNotFoundException;
import java.io.PrintWriter;
import java.util.Scanner;

public class Main {
    public static void main(String args[]){
        MyFrame myframe=new MyFrame();
    }
}
//============================================================================================================

class MyFrame extends JFrame implements ActionListener{

    JScrollPane scrollPane;
    JTextArea textArea;
    JComboBox fonts;
    JComboBox fontStyles;
    JLabel fontSize;
    JSpinner spinner;
    JButton colorButton;

    JMenuBar menuBar;
    JMenu fileMenu;
    JMenuItem open;
    JMenuItem save;
    JMenuItem exit;



    MyFrame(){
        this.setSize(600,600);
        this.setLayout(new FlowLayout());
        this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        this.setLocationRelativeTo(null);
        this.getContentPane().setBackground(Color.LIGHT_GRAY);
        this.setTitle("Text Editor");

        textArea=new JTextArea();
        textArea.setFont(new Font("Arial",Font.PLAIN,20));
        textArea.setWrapStyleWord(true);
        textArea.setLineWrap(true);
        textArea.setForeground(Color.BLACK);

        scrollPane=new JScrollPane(textArea);
        scrollPane.setPreferredSize(new Dimension(550,500));

        String List[]=GraphicsEnvironment.getLocalGraphicsEnvironment().getAvailableFontFamilyNames();
        fonts=new JComboBox<>(List);
        fonts.setSelectedItem("Arial");
        fonts.addActionListener(this);

        String list2[]={"Plain","Bold","Italic","Bold Italic"};
        fontStyles=new JComboBox<>(list2);
        fontStyles.setSelectedItem("Plain");
        fontStyles.addActionListener(this);


        fontSize=new JLabel("Size");

        spinner=new JSpinner();
        spinner.setValue(20);
        spinner.addChangeListener(new ChangeListener() {
            @Override
            public void stateChanged(ChangeEvent e) {textArea.setFont(new Font(textArea.getFont().getFamily(),textArea.getFont().getStyle(),(int)spinner.getValue()));}
        });

        colorButton=new JButton("Color");
        colorButton.setFocusable(false);
        colorButton.addActionListener(this);


        menuBar=new JMenuBar();
        fileMenu=new JMenu("File");
        open=new JMenuItem("Open file"); open.addActionListener(this);
        save=new JMenuItem("Save File"); save.addActionListener(this);
        exit=new JMenuItem("Exit");      exit.addActionListener(this);

        menuBar.add(fileMenu);
        fileMenu.add(open);
        fileMenu.add(save);
        fileMenu.add(exit);


        this.setJMenuBar(menuBar);
        this.add(fonts);
        this.add(fontStyles);
        this.add(colorButton);
        this.add(fontSize);
        this.add(spinner);
        this.add(scrollPane);
        this.setVisible(true);
    }
    @Override
    public void actionPerformed(ActionEvent e) {
        if(e.getSource()==colorButton){
            JColorChooser colorChooser=new JColorChooser();
            Color color=JColorChooser.showDialog(null,"Choose a color",Color.BLACK);
            textArea.setForeground(color);
        }
        //-----------------------------------------------------------------------
        if(e.getSource()==fonts){
            textArea.setFont(new Font((String)fonts.getSelectedItem(),(int)textArea.getFont().getStyle(),(int)textArea.getFont().getSize()));
        }
        //-----------------------------------------------------------------------
        if(e.getSource()==fontStyles){
            textArea.setFont(new Font((String)textArea.getFont().getFamily(),(int)fontStyles.getSelectedIndex(),textArea.getFont().getSize()));
        }
        //-----------------------------------------------------------------------
        if(e.getSource()==open){
            JFileChooser fileChooser=new JFileChooser();
            fileChooser.setCurrentDirectory(new File("."));
            FileNameExtensionFilter filter=new FileNameExtensionFilter("Text files","txt");
            fileChooser.setFileFilter(filter);

            int response=fileChooser.showOpenDialog(null);

            if(response==JFileChooser.APPROVE_OPTION){
                File file=new File(fileChooser.getSelectedFile().getAbsolutePath());
                Scanner scanner=null;
                try {
                    scanner=new Scanner(file);
                    if(file.isFile()){
                        while (scanner.hasNextLine()){
                            String line=scanner.nextLine()+"\n";
                            textArea.append(line);
                        }
                    }
                } catch (FileNotFoundException ex) {
                    throw new RuntimeException(ex);
                }
                finally {
                    scanner.close();
                }
            }
        }
        //-----------------------------------------------------------------------
        if(e.getSource()==save){
            JFileChooser fileChooser=new JFileChooser();
            fileChooser.setCurrentDirectory(new File("."));

            int response=fileChooser.showSaveDialog(null);
            if(response==JFileChooser.APPROVE_OPTION){
                File file=new File(fileChooser.getSelectedFile().getAbsolutePath());
                PrintWriter printWriter=null;
                try {
                    printWriter=new PrintWriter(file);
                    printWriter.println(textArea.getText());
                } catch (FileNotFoundException ex) {
                    throw new RuntimeException(ex);
                }
                finally {
                    printWriter.close();
                }
            }
        }
        //-----------------------------------------------------------------------
        if(e.getSource()==exit){System.exit(0);}
        //-----------------------------------------------------------------------
    }
}

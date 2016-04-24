# Hw1-IR
/*
 * To change this license header, choose License Headers in Project Properties.
 * To change this template file, choose Tools | Templates
 * and open the template in the editor.
 */
package assignment1;

import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.InputStreamReader;
import java.net.URL;
import java.util.ArrayList;
import java.util.LinkedHashSet;
import java.util.LinkedList;
import java.util.Queue;
import org.apache.commons.lang.StringEscapeUtils;
import org.htmlparser.Node;
import org.htmlparser.Parser;
import org.htmlparser.Tag;
import org.htmlparser.filters.TagNameFilter;
import org.htmlparser.util.NodeList;
import org.htmlparser.util.SimpleNodeIterator;
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;
/**
 *
 * @author sonyworld
 */
public class Hw1 {
    private ArrayList<String> links;  //new library define how is the list is there
    private ArrayList<String> crawled;
    public static int count = 0;
    public Hw1() {
        this.links = new ArrayList<String> (); // ALLOCATING MEMORY inaciate asn oblect
        this.crawled = new ArrayList<String>();
    }
    
    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {
       Hw1 hw= new Hw1();
       String url="https://en.wikipedia.org/wiki/Sustainable_energy";
  //     hw.crawlWiki(url,0); //#1
//       hw.crawlWikiFocusedBFS(url, "solar", 0); //#2
       hw.crawlWikiFocusedDFS(url, "solar", 0); //#3
       hw.writeFile();
    }
    
    public void crawlWiki(String url, int depth){
        if(depth==5){
            return ;
        }
        
        ArrayList<String> returnLinks= new ArrayList<String>();
       try {
       
            URL my_url = new URL(url);
//            Thread.sleep(1000); // Adding 1 sec delay
            BufferedReader br = new BufferedReader(new InputStreamReader(my_url.openStream()));
            String strTemp = "";
            String content = "";
            // Read the page from the URL to a String
            while(null != (strTemp = br.readLine())){
                //System.out.println(strTemp);
                content = content + strTemp + "\n";

            }
            Parser p= new Parser(content);
            returnLinks = parseFile(p);
            depth++;
            for(String page: returnLinks){
                if (!crawled.contains(page)) {
                    count++;
                    System.out.println(page);
                    links.add(page);
                    crawled.add(page);
                    System.out.println("Count : " + count + " Depth : " + depth);
                    crawlWiki(page,depth);
                    Thread.sleep(1000);// 1s delay
                    if (count == 1000) {
                        System.out.println("Done Crawling");
                        writeFile();
                        System.exit(0);
                    }
                }
            }
       } catch(Exception ex){
           ex.printStackTrace();
           
       } 
    }
    
    
    /**
     * Function to crawl Wiki with the key phrase using DFS approach
     * @param url
     * @param key
     * @param depth 
     */
    public void crawlWikiFocusedDFS(String url, String key, int depth){
        if(depth==5){
            return ;
        }
        
        ArrayList<String> returnLinks= new ArrayList<String>();
       try {
       
            URL my_url = new URL(url);
            BufferedReader br = new BufferedReader(new InputStreamReader(my_url.openStream()));
            String strTemp = "";
            String content = "";
            // Read the page from the URL to a String
            while(null != (strTemp = br.readLine())){
                content = content + strTemp + "\n";

            }
            Parser p = new Parser(content);
            returnLinks = parseFile(p);
            depth++;
            for(String page: returnLinks){
//                String rawText = Jsoup.connect(page).get().select("div[id=content]").first().text();
                String rawText = getWikiText(Jsoup.connect(page).get());
//                int retrIdx = rawText.indexOf("Retrieved from \"https://en.wikipedia.org/w/index.php?");
//                rawText = rawText.substring(0, retrIdx);
                if ((page.contains(key) || rawText.contains(key)) && !crawled.contains(page)) {
                    count++;
                    System.out.println(page);
                    links.add(page);
                    crawled.add(page);
//                    System.out.println(rawText.substring(rawText.indexOf(key)-40, rawText.indexOf(key)+100) );
                    System.out.println("Count : " + count + " Depth : " + depth);
                    crawlWikiFocusedDFS(page, key, depth);
                    rawText = "";
                    Thread.sleep(1000);// 1s delay
                    if (count == 1000) {
                        System.out.println("Done Crawling");
                        writeFile();
                        System.exit(0);
                    }
                }
            }
       } catch(Exception ex){
           ex.printStackTrace();
           
       } 
    }
    
    
    
    /**
     * Function to crawl Wiki with the key phrase using BFS approach
     * @param url
     * @param key
     * @param depth 
     */
    public void crawlWikiFocusedBFS(String url, String key, int depth) {
        Queue<String> queue = new LinkedList<String>();
	LinkedHashSet<String> marked = new LinkedHashSet<String>();
        if(depth==5){
            return ;
        }
        queue.add(url);
        ArrayList<String> returnLinks= new ArrayList<String>();
        try {
            while(!queue.isEmpty()) {
                String crawled_url = queue.remove();
                marked.add(crawled_url);
                URL my_url = new URL(crawled_url);
                BufferedReader br = new BufferedReader(new InputStreamReader(my_url.openStream()));
                String strTemp = "";
                String content = "";
                // Read the page from the URL to a String
                while(null != (strTemp = br.readLine())){
                    content = content + strTemp + "\n";

                }
                Parser p = new Parser(content);
                returnLinks = parseFile(p);
                depth++;
                for(String page: returnLinks){
    //                String rawText = Jsoup.connect(page).get().select("div[id=content]").first().text();
                    String rawText = getWikiText(Jsoup.connect(page).get());
    //                int retrIdx = rawText.indexOf("Retrieved from \"https://en.wikipedia.org/w/index.php?");
    //                rawText = rawText.substring(0, retrIdx);
                    if ((page.contains(key) || rawText.contains(key)) && !marked.contains(page)) {
                        count++;
                        System.out.println(page);
                        queue.add(page);
                        links.add(page);
    //                    System.out.println(rawText.substring(rawText.indexOf(key)-40, rawText.indexOf(key)+100) );
                        System.out.println("Count : " + count + " Depth : " + depth);
                        rawText = "";
                        Thread.sleep(1000);// 1s delay
                        if (count == 1000) {
                            System.out.println("Done Crawling");
                            writeFile();
                            System.exit(0);
                        }
                    }
                }
            }
       } catch(Exception ex){
           ex.printStackTrace();
           
       } 
    }
    
    public String getWikiText(Document doc) {
        String retTxt = "";
        Elements paragraphs = doc.select(".mw-content-ltr p");
        Element firstParagraph = paragraphs.first();
        Element lastParagraph = paragraphs.last();
        Element p;
        int i=1;
        p=firstParagraph;
        retTxt = retTxt + p.text();
//        System.out.println(p.text());
        while (p!=lastParagraph){
            p = paragraphs.get(i);
            retTxt = retTxt + p.text();
//            System.out.println(p.text());
            i++;
        }
        return retTxt;
    }
    
    public ArrayList<String> parseFile(Parser p){
        ArrayList<String> returnLinks= new ArrayList<String>();
         try{

                NodeList nodeList = p.parse(new TagNameFilter("a"));
                SimpleNodeIterator nodeIterator = nodeList.elements();
                while(nodeIterator.hasMoreNodes()){
                    Node node = nodeIterator.nextNode();
                    Tag t = (Tag)node;
//                    System.out.println(t.toHtml());
                    String TagString= t.toHtml();
                    if(TagString.contains("href=\"") && !TagString.contains(":")&& !TagString.contains("#")){
                        int index = TagString.indexOf("href=\"");
                        int index2 = TagString.indexOf("\"", index+6);
                        String Title = TagString.substring(index+6,index2);
    //                    TagString = TagString.substring(index+6, index2);
                        TagString = StringEscapeUtils.unescapeHtml(TagString).trim();
    //                    System.out.println(TagString.startsWith("wiki"));
                        if(Title.startsWith("/wiki") && !Title.contains("Main_Page")){
//                            System.out.println(Title);
                            returnLinks.add("https://en.wikipedia.org"+Title);
                        }
                    }
                }               
        }catch(Exception e){
            e.printStackTrace();
        }
    
        return returnLinks;
       
    }
    
    
     public void writeFile(){
        try{
        // Create file 
            FileWriter fstream = new FileWriter("C:\\Users\\sonyworld\\Desktop\\crses\\IR2\\Hw1\\Crawl3.txt");
            BufferedWriter out = new BufferedWriter(fstream);
            for(String link:links){
                out.write(link+"\n");
            }
            
            //Close the output stream
            out.close();
        }catch (Exception e){//Catch exception if any
            System.err.println("Error: " + e.getMessage());
        }
    }
}

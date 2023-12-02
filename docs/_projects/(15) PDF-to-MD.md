---
name: PDFtoMD
tags: [PDF Conversion, Markdown, Note Organization]
tools: [C#, .NET]
image: ../assets/PDFtoMD/PDFtoMD_screenshot.png
description: A tool to convert PDF files, specifically from Apple Notes via Wondershare Dr.Fone, into Markdown format for better note organization.
---

# **📝 PDFtoMD 📝**

**[Github Link](https://github.com/Sam-Hedges/PDFtoMD)**

---

PDFtoMD is a small utility I designed to transform PDF files into Markdown (.md) documents. Developed to manage and reformat PDFs downloaded from Apple Notes using Wondershare Dr.Fone, this tool simplifies the process of organizing notes by converting them into a more editable and manageable format.

The primary function of PDFtoMD is to remove the header and footer text added by the Dr.Fone tool during the PDF creation process and to convert these PDFs into Markdown files. This conversion was crucial as I moved over to managing my notes in a text-based format, offering greater flexibility and compatibility with various text editors and note-taking applications. What pushed me to do this was seeing other implementations of note taking using tools such as 

---

### **Inspiration**

---

My journey to creating PDFtoMD began with a quest for a more efficient and sustainable method of note-taking. Like many, I was drawn to the digital realm for organizing my thoughts and ideas. My exploration led me to various platforms and tools, each offering unique approaches to note management. It was through watching YouTube videos about note-taking systems using tools like Neovim, QOwnNotes, and Obsidian that I found my inspiration. These platforms demonstrated the power of digital note-taking in a way that resonated with my needs for accessibility and organization.

For years, my primary note-taking tool had been my iPhone. It was convenient and always at hand, but it lacked the flexibility and accessibility I needed, and the loose nature of the notes meant that it was harder to keep myself organised. I wanted my notes to be like a second brain of information, accessible across various platforms and easily manageable. This desire led me to seek a method to migrate my notes from the iPhone into a format that aligned with these aspirations.

My familiarity with Markdown, cultivated through the creation of my portfolio site using a Jekyll framework, played a pivotal role in this transition. Markdown's simplicity and versatility made it an ideal choice for my notes. It was a format I had come to appreciate for its clarity and ease of use. After considering various platforms, I settled on Obsidian. Its multi-platform nature (supporting both iPhone and Windows) and the extensibility offered by community-made extensions made it the perfect fit for my needs.

The challenge then was to convert my existing notes into this new format. The notes, exported from my iPhone via Wondershare Dr.Fone, were in PDF format – not ideal for the kind of fluid, interconnected digital workspace I envisioned with Obsidian. Thus, PDFtoMD was created out of necessity. It was made to bridge this gap, to transform these static PDF files into dynamic Markdown documents, ready to be integrated into my new digital note-taking ecosystem.

---

### **Functionality**

---

The tool is built using C# and targets the .NET framework. PDFtoMD operates by scanning a specified directory for PDF files and then processes each file individually. It extracts the text content from the PDFs, cleans up the formatting by removing unwanted headers and footers, and then converts the content into Markdown format. The resulting Markdown files are saved in a separate output directory, ready for use.

This is achieved through the use of regular expressions (regex), a powerful tool for text processing and manipulation.

- The first step in the cleanup process was to identify the common patterns in the headers and footers added by the Wondershare Dr.Fone tool. These patterns typically included repetitive text elements, timestamps, and other identifiable markers that were consistently present across the PDF files.

- Once these patterns were identified, I crafted specific regex patterns to match these unwanted text segments. Regex allows for precise and flexible pattern matching, making it an ideal choice for this task. The patterns were designed to be robust enough to handle variations in the text while ensuring that only the unwanted parts were targeted for removal.

- In the `PDFtoMD` program, these regex patterns were implemented within the text extraction process. As the text was extracted from each PDF page, the regex engine scanned through the content, identifying and capturing the segments that matched the predefined patterns.

- After capturing these segments, the next step was to remove them from the extracted text. This was done by replacing the matched patterns with an empty string, effectively erasing the unwanted headers and footers from the content. The result was a cleaner and more focused text output, free from the extraneous information that was not part of the original notes.

- The regex patterns were fine-tuned through testing and iteration to ensure maximum accuracy and efficiency. This process involved testing the tool with a variety of PDF files and adjusting the regex as needed to accommodate different formats and text layouts encountered in the PDFs.


---

### **Usage**

---

Users can easily set up PDFtoMD by defining the input and output directories in the `Program.cs` file. Once configured, the tool will process all PDF files in the input directory and output the converted Markdown files in the designated output directory. It is run within an IDE because the use case I had was very niche, and programming a user interface wasn't even a priority. I just needed to convert my PDFs and as such it is a very basic tool.

---

### **Implementation**

---

```csharp
using System.Text;
using System.Text.RegularExpressions;
using iTextSharp.text.pdf;
using iTextSharp.text.pdf.parser;
using Path = System.IO.Path;

namespace PDFtoMD;

internal static class Program
{
    private const string InputDirectory = @"C:\Games Development\C#\PDFtoMD\PDFtoMD\Input";
    private const string OutputDirectory = @"C:\Games Development\C#\PDFtoMD\PDFtoMD\Output";

    private static void Main()
    {
        foreach (string filePath in Directory.GetFiles(InputDirectory, "*.pdf"))
        {
            try
            {
                ConvertPdfToMarkdown(filePath, OutputDirectory);
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Error processing {filePath}: {ex.Message}");
            }
        }
    }

    private static void ConvertPdfToMarkdown(string filePath, string outputDirectory)
    {
        using PdfReader reader = new PdfReader(filePath);
        StringBuilder cleanedText = new StringBuilder();

        for (int i = 1; i <= reader.NumberOfPages; i++)
        {
            string text = PdfTextExtractor.GetTextFromPage(reader, i);
            cleanedText.Append(CleanText(text));
        }

        using StreamWriter writer = new StreamWriter(Path.Combine(outputDirectory, Path.GetFileNameWithoutExtension(filePath) + ".md"));
        writer.Write(cleanedText.ToString());
    }
    
    private static string CleanText(string text)
    {
        text = Regex.Replace(text, @"\bNotes\b", "");
        text = Regex.Replace(text, @"Dr\.Fone iPhone \(210\) \d{2}/\d{2}/\d{4} \d{2}:\d{2}:\d{2} (AM|PM)", "");
        text = Regex.Replace(text, @"(.+?)\s+\d{4}/\d{2}/\d{2} \d{2}:\d{2}:\d{2}\s+\1", "");
        return text;
    }
}
```


---

### **Future Aspirations: From Tool to Plugin**

---

Looking ahead, I envision taking PDFtoMD to the next level. My goal is to evolve it from a standalone tool into a comprehensive solution that can directly extract notes from an iPhone and convert them into Markdown format, specifically tailored for Obsidian. Inspired by my recent uptake of Obsidian, my idea is to create a plugin that seamlessly integrates apple notes on iPhone, into the platform, simplifying the process to migrate notes for end users. This development would not only benefit my personal workflow but also offer a valuable resource to others seeking to optimize their digital note-taking practices.

---


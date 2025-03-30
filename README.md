# GDrive-Folders-Organizer

## File Organization in Google Drive
This Google Colab notebook helps organize files in a Google Drive directory based on their file extensions. Files will be sorted into predefined categories depending on their types (documents, multimedia files, code files, etc.).

## Requirements
This script runs in Google Colab and requires the following libraries:
- `os` (built-in)
- `shutil` (built-in)
- `tqdm`

## Instructions
### Step 1: Mount Google Drive folder to Google Colab
Before starting file organization, mount your Google Drive folder to Google Colab by running the following code:

```python
from google.colab import drive
drive.mount('/content/drive')
```

### Step 2: Import required libraries
```python
import os
import shutil
from tqdm import tqdm
```

### Step 3: Get Unique File Extensions
The `get_unique_file_extensions` function walks through the directory structure of a given source folder and identifies unique file extensions.

```python
def get_unique_file_extensions(src_folder):
    unique_file_extensions = set()

    for root, _, files in os.walk(src_folder):
        for filename in files:
            file_ext = os.path.splitext(filename)[1]
            unique_file_extensions.add(file_ext)

    return unique_file_extensions

if __name__ == "__main__":
    source_folder = "/content/drive/MyDrive"
    unique_extensions = get_unique_file_extensions(source_folder)
    print(unique_extensions)
```

### Step 4: Define File Categories
Define a dictionary mapping file extensions to categories. This will serve as the guide for the categorization process.

```python
file_categories = {
    'Office/Documents': ['.txt', '.pdf', '.doc', '.docx', '.rtf', '.gdoc', '.odt'],
    'Office/Presentations': ['.ppt', '.pptx', '.gslides', '.odp', '.key'],
    'Office/Spreadsheets': ['.xls', '.xlsx', '.gsheet', '.ods', '.numbers', '.csv'],
    'Coding&Data/Code': ['.xml', '.json', '.php', '.sh', '.cpp', '.js', '.pde', '.java', '.cs', '.c', '.h', '.go', '.rb', '.pl', '.swift', '.ts'],
    'Coding&Data/Python': ['.py', '.pyc'],
    'Coding&Data/HTML&CSS': ['.html', '.css', '.scss', '.sass', '.less'],
    'Coding&Data/Jupyter_Notebooks': ['.ipynb'],
    'Coding&Data/Database_Files': ['.db', '.sql', '.sqlite', '.accdb'],
    'Multimedia/Videos': ['.mp4', '.mkv', '.flv', '.avi', '.mov', '.wmv'],
    'Multimedia/Images': ['.svg', '.png', '.jpg', '.jpeg', '.gif', '.bmp', '.ico', '.tiff', '.webp'],
    'Multimedia/Audio': ['.mp3', '.wav', '.ogg', '.flac', '.m4a', '.aac'],
    'Multimedia/Fonts': ['.ttf', '.otf', '.woff', '.woff2', '.eot'],
    'Multimedia/3D_Models': ['.obj', '.fbx', '.dae', '.3ds', '.blend'],
    'Multimedia/Vector_Graphics': ['.ai', '.eps', '.sketch'],
    'Multimedia/CAD_Files': ['.dwg', '.dxf'],
    'Google_Workspace/Drawings': ['.gdraw'],
    'Google_Workspace/Sites': ['.gsite'],
    'Google_Workspace/Forms': ['.gform'],
    'Google_Workspace/Jamboard': ['.gjam'],
    'Google_Workspace/Scripts': ['.gscript'],
    'Others/Shortcuts': ['.lnk'],
    'Others/Markdown': ['.md', '.markdown'],
    'Others/PGP_Keys': ['.asc'],
    'Others/Python_Code': ['.pyc', '.pyo'],
    'Others/Archives': ['.zip', '.tar', '.rar', '.7z', '.gz', '.bz2'],
    'Others/eBooks': ['.epub', '.mobi', '.azw', '.azw3'],
    'Others': [''],
}
```

### Step 5: Categorize Files
The `categorize_files` function goes through the directory structure, moving files into the appropriate category folders based on their extensions.

```python
def categorize_files(src_folder, file_categories=file_categories, go_through=True):

    def move_file_to_category(file_path, filename, file_ext):
        for category, extensions in file_categories.items():
            if file_ext in extensions:
                dest_folder = os.path.join(src_folder, category)
                break
        else:
            dest_folder = os.path.join(src_folder, 'Others')

        os.makedirs(dest_folder, exist_ok=True)
        shutil.move(file_path, os.path.join(dest_folder, filename))

    def process_files(root, files):
        for filename in tqdm(files, desc=f"Processing files in {root}", unit="file"):
            file_path = os.path.join(root, filename)

            if os.path.isdir(file_path):
                continue

            file_ext = os.path.splitext(file_path)[1]
            move_file_to_category(file_path, filename, file_ext)
        if go_through:
            try:
                os.rmdir(root)
                print(f"Successful deleting folder: {root}")
            except OSError:
                pass

    if go_through:
        folder_iterator = os.walk(src_folder)
    else:
        folder_iterator = [(src_folder, [], os.listdir(src_folder))]

    [process_files(root, files) for root, _, files in folder_iterator]
```

### Step 6: Implement File Sorting
Finally, run the `categorize_files` function to sort all files in your Google Drive:

```python
source_folder = "/content/drive/MyDrive"
categorize_files(source_folder)
```

This will sort files into category-specific directories within the source folder. Please note that any files with extensions not defined in the `file_categories` dictionary will be moved to an 'Others' directory.

## Caution
Please **backup your data** before running this script, as moving files could result in data loss if not handled properly. We are not responsible for any data loss that may occur.

## Contributions
Suggestions and improvements are welcome. Feel free to submit a pull request.


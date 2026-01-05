import os
import shutil
from pathlib import Path

class FileOrganizer:
    def __init__(self, source_dir, dest_dir):
        self.source_dir = Path(source_dir)
        self.dest_dir = Path(dest_dir)
        self.dest_dir.mkdir(exist_ok=True)
        
        # Define file type mappings
        self.file_types = {
            'images': ['.jpg', '.jpeg', '.png', '.gif', '.bmp', '.svg'],
            'documents': ['.pdf', '.docx', '.txt', '.doc', '.rtf', '.odt'],
            'videos': ['.mp4', '.avi', '.mkv', '.mov', '.wmv'],
            'audio': ['.mp3', '.wav', '.flac', '.aac', '.ogg'],
            'archives': ['.zip', '.rar', '.7z', '.tar', '.gz'],
            'code': ['.py', '.js', '.java', '.cpp', '.c', '.html', '.css'],
            'spreadsheets': ['.xlsx', '.xls', '.csv', '.ods']
        }
    
    def organize_files(self):
        """Organize files in source directory into destination directories."""
        print(f"Starting organization in {self.source_dir}")
        processed = 0
        
        for file_path in self.source_dir.iterdir():
            if file_path.is_file():
                file_ext = file_path.suffix.lower()
                
                # Find appropriate destination folder
                dest_folder = None
                for folder, extensions in self.file_types.items():
                    if file_ext in extensions:
                        dest_folder = self.dest_dir / folder
                        break
                
                # Create destination folder if needed
                if dest_folder:
                    dest_folder.mkdir(exist_ok=True)
                    
                    # Move file
                    dest_path = dest_folder / file_path.name
                    if not dest_path.exists():
                        shutil.move(str(file_path), str(dest_path))
                        print(f"Moved {file_path.name} to {folder}/")
                        processed += 1
                    else:
                        print(f"Skipped {file_path.name} - already exists")
                else:
                    print(f"Skipped {file_path.name} - unknown file type")
        
        print(f"Organization complete. Processed {processed} files.")
    
    def create_report(self):
        """Generate a report of organized files."""
        report = []
        report.append("File Organization Report")
        report.append("========================")
        report.append(f"Source: {self.source_dir}")
        report.append(f"Destination: {self.dest_dir}\n")
        
        # Count files by type
        counts = {folder: 0 for folder in self.file_types.keys()}
        
        for folder in self.dest_dir.iterdir():
            if folder.is_dir():
                count = sum(1 for _ in folder.iterdir())
                counts[folder.name] = count
                report.append(f"{folder.name}: {count} files")
        
        # Calculate statistics
        total_files = sum(counts.values())
        report.append(f"\nTotal files moved: {total_files}")
        report.append(f"Total folders created: {len([f for f in self.dest_dir.iterdir() if f.is_dir()])}")
        
        # Write report to file
        report_path = self.dest_dir / "report.txt"
        with open(report_path, 'w') as f:
            f.write('\n'.join(report))
        
        print(f"Report generated: {report_path}")

def main():
    source = input("Enter source directory: ")
    dest = input("Enter destination directory: ")
    
    if not os.path.exists(source):
        print(f"Source directory {source} does not exist")
        return
    
    organizer = FileOrganizer(source, dest)
    organizer.organize_files()
    organizer.create_report()

if __name__ == '__main__':
    main()

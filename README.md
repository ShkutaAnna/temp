# temp

```javascript
parseFiles(files: File[]) {
  const parsePromises = files.map(file => this.parseExcel(file));

  Promise.all(parsePromises).then(parsedResults => {
    console.log('All files have been parsed:', parsedResults);
    this.processParsedData(parsedResults); // Your final action
  }).catch(error => {
    console.error('Error parsing files:', error);
  });
}

parseExcel(file: File): Promise<any> {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onload = (event: ProgressEvent<FileReader>) => {
      if (event.target?.result) {
        const data = event.target.result;
        // Parse the Excel data here (e.g., using XLSX library)
        resolve(data);
      } else {
        reject(new Error('Failed to read file'));
      }
    };
    reader.onerror = () => reject(new Error('File read error'));
    reader.readAsArrayBuffer(file); // Or `readAsBinaryString` depending on parsing library
  });
}
addItem(item: any) {
    this.store.dispatch(addItem({ item }));
  }

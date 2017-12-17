---
title:  "Decompressing in Java"
date:   2016-09-22 11:30:23
categories: [Java]
tags: [programming]
---
**Scenario**: There are one or multiple zip files present in a directory. The objective is to select only zip files, decompress those and put it in another directory.

This program can also be used to select files of a particular extention type.

Here is the code

```java
package com.cs.unzipexample;
/**
 * This program is for decompress only zip files of a directory to a particular folder
 *
 */

import java.io.File;
import java.io.FileOutputStream;
import java.io.FilenameFilter;
import java.io.IOException;
import java.io.InputStream;
import java.util.ArrayList;
import java.util.Enumeration;
import java.util.zip.ZipEntry;
import java.util.zip.ZipFile;

public class DecompressZipFileOnly {
	public static void main(String[] args) throws IOException {
		String source = "J:\\jyotiranjan\\Testdata";
		File dir = new File(source);

		File[] listZipFiles = dir.listFiles(new FilenameFilter() {
			public boolean accept(File dir, String filename)
			{
				return filename.endsWith(".zip");
			}
		} );

		System.out.println("Going to Unzip Only Zip Files");
		unZipFiles(listZipFiles);

	}

	private static void unZipFiles(File[] listZipFiles) throws IOException
	{
		String unzipLocation = "J:\\jyotiranjan\\Testdata\\temp";
		FileOutputStream fos = null;
		for (File zipFile : listZipFiles) {
			try {
				ZipFile zf=new ZipFile(zipFile);
				Enumeration<?> files = zf.entries();
				File f = null;
				ArrayList fileList=new ArrayList();
				while (files.hasMoreElements()) {
					ZipEntry entry = (ZipEntry) files.nextElement();
					System.out.println("Entry: " +entry);

					InputStream eis = zf.getInputStream(entry);
					byte[] buffer = new byte[1024];
					int bytesRead = 0;

					f = new File(unzipLocation + File.separator + entry.getName());
					fileList.add(f);
					f.createNewFile();

					fos = new FileOutputStream(f);

					while ((bytesRead = eis.read(buffer)) != -1) {
						fos.write(buffer, 0, bytesRead);
					}
				}
			} catch (IOException e) {
				e.printStackTrace();
				continue;
			} finally {
				if (fos != null) {
					try {
						fos.close();
					} catch (IOException e) {
						// ignore
					}
				}
			}
		}
	}
}
```

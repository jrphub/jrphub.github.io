---
title:  "String to Date Conversion in Java"
date:   2016-12-05 11:30:23
categories: [Java]
tags: [programming]
---
Program :

```java
public class StringToDate {

	public static void main(String[] args) {
		String dateStr1 = "Mon Monday,11 11 Nov November, 11 11, 13 13 2013, 11:32:34 AM IST";
		SimpleDateFormat date1 = new SimpleDateFormat("E EEEE,MM M MMM MMMM, d dd, y yy yyyy, h:m:s a z" , Locale.ENGLISH);
		try {
			Date formattedDate1 = date1.parse(dateStr1);
			System.out.println(dateStr1+"---->"+formattedDate1);
		} catch (ParseException e) {
			e.printStackTrace();
		}

	}

}
/*SimpleDateFormat can be used to control the date/time display format:
E (day of week): 3E or fewer (in text xxx), >3E (in full text)
M (month): M (in number), MM (in number with leading zero)
3M: (in text xxx), >3M: (in full text full)
d or dd for date of month
y or yy for 2 digit year, yyyy for 4 digit year
h (hour): h, hh (with leading zero)
m (minute)
s (second)
a (AM/PM)
H (hour in 0 to 23)
z (time zone)*/
```

Output :

```java
Mon Monday,11 11 Nov November, 11 11, 13 13 2013, 11:32:34 AM IST---->Mon Nov 11 11:32:34 IST 2013
```


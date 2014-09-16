---
author: gvivekcbe
comments: true
date: 2011-06-12 18:40:31+00:00
layout: post
slug: mfc-most-frustrating-creation
title: MFC - Most frustrating creation
wordpress_id: 12
---

**How to break the class wizard using namespaces:**

****When i started programming in MFC, i was disturbed to find how annoying different it was from other languages that i have used. There are two files that get generated when you create a MFC project.






	
  1. x

	
  2. y




Enclosing these classes with a namespace renders the class wizard useless. It doesn't list out the classes added to the project. Bottomline: Dont add namespaces to the MFC generated classes.





**string is a mess:**

****There are too many string types in MFC. string/Cstring/LPCTSTR. Most of the controls take LPCTSTR. So the string has to be encoded like this. MsgBox(L"Hey")

**Overload << to redirect output to your GUI:**

This was one trick that i used. I wanted a way to display all output to a GUI ListBox. I didnt want to call the listbox.addItem everytime. It is not as easy as calling cout <<. You can use the format specifiers and manipulators with this approach. So i created a ostream class and overloaded << to have it display on the listbox.

[sourcecode language="cpp"]
<pre>class CDpFpgaToolDlg;

namespace DpFpgaTool 
{
class DpFpgaLog : public std::strstream
{
public:

	static DpFpgaLog& GetInstance()
	{
		static DpFpgaLog log;
		return log;
	}

	void Write();
	friend std::ostream& endm( std::ostream& msg );

	

private:
	DpFpgaLog() : std::strstream( buffer = new char[BUFLEN], BUFLEN, std::ios::out)
	{
	}

	~DpFpgaLog()
	{
		delete[] buffer;
	}

	DpFpgaLog(const DpFpgaLog& log)
	{
	}

	DpFpgaLog& operator = (const DpFpgaLog& log)
	{
	}

	char *buffer;
	static const unsigned int BUFLEN = 1024;
	
};

std::ostream& endm(std::ostream& msg);
void ReportError( LPCTSTR val);

}</pre>
[/sourcecode]



In the .cpp files, theses functions are defined.

[sourcecode language="cpp"]
<pre>namespace DpFpgaTool
{
	void DpFpgaLog::Write()
	{
		*this<<ends;
		std::ostringstream stream;
		stream<<buffer;
		CString displayStr(stream.str().c_str());
		CDpFpgaToolDlg& dlg = CDpFpgaToolDlg::GetInstance();
		dlg.m_listBox.AddString(displayStr);
		this->seekp(0);
	}

	ostream& endm(ostream& msg)
	{
		dynamic_cast<DpFpgaLog*>(&msg)->Write();	
		return msg;
	}

	
	void ReportError( LPCTSTR val)
	{
		CDpFpgaToolDlg& dlg = CDpFpgaToolDlg::GetInstance();
		dlg.DisplayError(val);
		exit(1);
	}

}</pre>
[/sourcecode]

---
author: gvivekcbe
comments: true
date: 2011-06-12 18:45:34+00:00
layout: post
slug: thread-manager-pattern
title: Thread Manager pattern
wordpress_id: 24
---

A thread manager class that i wrote. For WIN32 threads.

[sourcecode language="cpp"]
#pragma once
#include <iostream>
#include <map>

#ifdef WIN32
	#define WINAPI __stdcall
#else
	#define WINAPI 
#endif

typedef void* HANDLE;
//Enum to id thread
typedef enum _ThreadTypes_
{
	THREADTYPE_1
}ThreadTypes;

//Base class for thread implementation
class CUnitThread
{
public:
	CUnitThread(const std::string desc, const ThreadTypes thrdType, const bool isThdActive)
		:description(desc), thdType(thrdType), isActive(isThdActive)
	{ }
	virtual ~CUnitThread(){}; 

	virtual unsigned long Execute() = 0;
	bool CheckIfActive() const { return isActive; }
	void SetActive( const bool isThdactive) { isActive = isThdactive; }
	ThreadTypes GetThreadType() const { return thdType; }

	const std::string GetDesc() const { return description; }

private:
	const std::string description;
	const ThreadTypes thdType;
	bool isActive;	
};

//ThreadManager which creates the threads
class CThreadManager
{
public:
	static CThreadManager& Instance();

	unsigned long RunThread(CUnitThread* const unitThread);
	unsigned long StopThread(const ThreadTypes threadType);
	unsigned long SuspendThreadExecution( const ThreadTypes threadType);
	unsigned long ResumeThreadExecution( const ThreadTypes threadType);
	unsigned long ShutDownThreads();
	
	static unsigned int WINAPI ExecuteThread(void* param);

private:
	CThreadManager();
	~CThreadManager();
	CThreadManager(const CThreadManager&){}
	CThreadManager& operator = (const CThreadManager&){}

	void AddThread( CUnitThread* const threadT, HANDLE hnd);
	void RemoveThread( const ThreadTypes threadType);
	void CloseThreadHandles( std::pair<CUnitThread* const, HANDLE>  itr );

	HANDLE FindThreadHandle(const ThreadTypes threadType) const;
	CUnitThread* const FindThreadObject( const ThreadTypes threadType ) const;

	typedef std::map<CUnitThread* const, HANDLE> ThreadTable;
	typedef ThreadTable::const_iterator ThreadTableItr;
	ThreadTable threadTable;

	static const unsigned long MAXTIMEOUT = 3000;

};
[/sourcecode]

The definitions in .cpp file:

[sourcecode language="cpp"]

#include "ThreadManager.h"
#include <algorithm>

using namespace std;
#ifdef WIN32
#include <process.h>
#include <Windows.h>
#endif

CThreadManager& CThreadManager::Instance()
{
	static CThreadManager threadManager;
	return threadManager;
}

unsigned long CThreadManager::RunThread( CUnitThread* const unitThread )
{
#ifdef WIN32
	MsgAssert( unitThread != NULL);
	unsigned long retVal = OK;
	if(FindThreadHandle(unitThread->GetThreadType()) == NULL)
	{
		unsigned int threadId = 0;
		HANDLE thdHnd = (HANDLE)_beginthreadex(NULL, 0, CThreadManager::ExecuteThread, unitThread, 0, &threadId  );
		MsgAssert(thdHnd!=NULL);
		
		AddThread( unitThread, thdHnd);
	}
	else
	{
		LogStream threadMsg;
		threadMsg<<"Error in attempting to launch "<<unitThread->GetDesc()<<" thread again"
			<<endl;
		retVal = ERROR;
	}

	return retVal;
#else
	return OK;
#endif
}

CThreadManager::CThreadManager()
{
	
}

CThreadManager::~CThreadManager()
{
#ifdef WIN32
	//Close handles
	for_each( threadTable.begin(), threadTable.end(), 
			bind1st(mem_fun(&CThreadManager::CloseThreadHandles) ,this)  );
#endif
}

void CThreadManager::CloseThreadHandles( std::pair<CUnitThread* const, HANDLE>  itr )
{
	CloseHandle(itr.second);
}

HANDLE CThreadManager::FindThreadHandle( const ThreadTypes threadType ) const
{
	for(ThreadTableItr it = threadTable.begin(); it != threadTable.end(); it++ )
	{
		CUnitThread* const unitThread = it->first;
		if( unitThread->GetThreadType() == threadType)
		{
			return it->second;
		}
	}
	return NULL;
}

CUnitThread* const CThreadManager::FindThreadObject(const ThreadTypes threadType) const
{
	for(ThreadTableItr it = threadTable.begin(); it != threadTable.end(); it++ )
	{
		CUnitThread* const unitThread = it->first;
		if( unitThread->GetThreadType() == threadType)
		{
			return it->first;
		}
	}
	return NULL;
}

void CThreadManager::AddThread( CUnitThread* const threadObj, HANDLE threadHnd )
{
	MsgAssert( threadObj!=NULL && threadHnd!=NULL );
	threadTable[threadObj] = threadHnd;
}

unsigned long CThreadManager::StopThread( const ThreadTypes threadType )
{
#ifdef WIN32
	unsigned long retVal = OK;
	CUnitThread* const threadObj = FindThreadObject(threadType);
	HANDLE const threadHnd = FindThreadHandle(threadType);
	if( threadObj!=NULL && threadHnd!=NULL )
	{
		threadObj->SetActive(false);

		unsigned long waitResult = WaitForSingleObject( threadHnd, MAXTIMEOUT);
		if ( waitResult == WAIT_TIMEOUT || waitResult == WAIT_FAILED)
		{
			retVal = ERROR;
			LogStream threadMsg;
			threadMsg<<"Error in stopping the thread: "<<threadObj->GetDesc()<<endl;
		}

		RemoveThread( threadType );
	}
	return retVal;
#else
	return OK;
#endif
}

unsigned long CThreadManager::SuspendThreadExecution( const ThreadTypes threadType )
{
#ifdef WIN32
	unsigned long retVal = OK;
	HANDLE const threadHnd = FindThreadHandle(threadType);
	MsgAssert( threadHnd!=NULL );
	unsigned long suspendStatus = SuspendThread( threadHnd );

	if ( suspendStatus == -1)
		retVal = ERROR;

	return retVal;
#else
	return OK;
#endif
}

unsigned long CThreadManager::ResumeThreadExecution( const ThreadTypes threadType )
{
#ifdef WIN32
	unsigned long retVal = OK;
	HANDLE const threadHnd = FindThreadHandle(threadType);
	MsgAssert(threadHnd != NULL);
	
	unsigned long resumeStatus = ResumeThread(threadHnd);	
	if( resumeStatus == -1)
		retVal = ERROR;
	return retVal;
#else
	return OK;
#endif
}

unsigned long CThreadManager::ShutDownThreads()
{
#ifdef WIN32
	unsigned long retVal =OK;
	for( ThreadTableItr itr = threadTable.begin(); itr!=threadTable.end(); itr++)
	{
		CUnitThread* const threadObj = itr->first;
		HANDLE const threadHnd = itr->second;
		if(threadObj != NULL && threadHnd !=NULL )
		{
			threadObj->SetActive(false);

			unsigned long waitResult = WaitForSingleObject( threadHnd, MAXTIMEOUT);
			if ( waitResult == WAIT_TIMEOUT || waitResult == WAIT_FAILED)
			{
				retVal = ERROR;
				LogStream threadMsg;
				threadMsg<<"Thread: "<<threadObj->GetDesc()<<" shutdown Failed"<<endl;
				break;
			}
			RemoveThread(threadObj->GetThreadType());
		}
	}	
	return retVal;
#else
	return OK;
#endif
}

unsigned int WINAPI CThreadManager::ExecuteThread( void* param )
{
	CUnitThread* const threadObj = static_cast<CUnitThread*>(param);
	MsgAssert(threadObj!=NULL);

	if( threadObj->CheckIfActive())
		threadObj->Execute();

	return 0;
}

void CThreadManager::RemoveThread( const ThreadTypes threadType )
{
	CUnitThread* const threadObj = FindThreadObject( threadType );
	HANDLE const threadHnd = FindThreadHandle( threadType );
	CloseHandle( threadHnd );
	threadTable.erase( threadObj );
}
[/sourcecode]



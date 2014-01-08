---
layout: post
title: Entity Framework - Generic repository Pattern - Part 1
category: entity-framework, pattern, .net, csharp 
author: ashutosh raina
email: ashutoshraina1989@gmail.com
series:
    name: Entity Framework Generic Repository Pattern
    current: 1
    part: Part 1 - Generic Repositry Pattern
    part: Part 2 - Generic Repositry Pattern Contd.
---
The generic repository pattern has been a matter of discussion on the forums and SO. Developers are not convinced that it solves a problem, yet everyday SO has more questions about the pattern. Clearly, something is not right. I tried to implement the pattern in a way I thought fit. I changed my mind a million times during the process, that tells you that the process was not smooth. You can find several implementations of the pattern scattered all over the internet. I will not pass a opinion on the pattern. I would rather tell you what issues I faced and I how solved (or not) them.
<!--excerpt-->
First I created an IRepository and IUnitOfwork
    
    public interface IRepository
    {
        T Add(T entity);
        T Remove(T entity);
        T Update(T entity);
        IQueryable Query();        
    }
    public interface IUnitOfWork : IDisposable
    {
        void Commit();
    }

Next, let's get the EF specific implementation up and running.

    /// <summary>
    /// A EFRepository represents the repository for performing operations on the
    /// Entity using the EntityFramework.
    /// </summary>
    /// <typeparam name="T">T is the Entity</typeparam>
    public class EFRepository<T> : IRepository<T> where T : class
    {
        /// <summary>
        /// This is set in the constructor and provides access to the underlying EntityFramework methods
        /// </summary>
        private DbSet<T> _dbSet;
        /// <summary>
        /// The context for working with the EntityFramework. This is set in the constructor.
        /// </summary>
        private DbContext _dataContext;
        /// <summary>
        /// Initialises a new instance of Repository for <see cref="T"/>
        /// </summary>
        /// <param name="unitOfWork">IUnitOfWork</param>
        /// <param name="dataContext">DbContext</param>
        /// <exception cref="ArgumentNullException">Throws ArgumentNullException if any of the arguments is null</exception>
        public EFRepository(IUnitOfWork unitOfWork, DbContext dataContext)
        {
            if (unitOfWork == null)
            {
                throw new ArgumentNullException("unitOfWork", "unitOfWork cannot be null");
            }
            if (dataContext == null)
            {
                throw new ArgumentNullException("dataContext", "dataContext cannot be null");
            }
            var EfUnitOfWork = unitOfWork as EFUnitOfWork;            
            _dataContext = dataContext;
            _dbSet = _dataContext.Set<T>();
        }
        /// <summary>
        /// Adds the specified Entity to the DbSet of the context.
        /// The Entity is inserted only when UnitOfWork is commited.
        /// </summary>
        /// <param name="item">The Entity to be added</param>
        /// <returns>The added Entity</returns>
        public T Add(T item)
        {
            return _dbSet.Add(item);
        }
        /// <summary>
        /// Removes the specified Entity from the DbSet of the context.
        /// The Entity is removed only when UnitOfWork is commited.
        /// </summary>
        /// <param name="item">The Entity to be removed</param>
        /// <returns>The Entity removed from the underlying DbSet</returns>
        public T Remove(T item)
        {
            return _dbSet.Remove(item);
        }
        /// <summary>
        /// Removes the specified Entity from the DbSet of the context.
        /// The Entity is removed only when UnitOfWork is commited.
        /// </summary>
        /// <param name="item">The Entity to be updated</param>
        /// <returns>the Entity removed from the underlying DbSet</returns>
        public T Update(T item)
        {
            var updated = _dbSet.Attach(item);
            _dataContext.Entry(item).State = EntityState.Modified;
            return updated;
        }
        /// <summary>
        /// Provides the caller with the underlying DbSet.
        /// </summary>
        /// <returns>An IQueryable to run queries against the underlying DbSet</returns>
        public IQueryable<T> Query()
        {
            return _dbSet;
        }

Follow this up with an implementation of EFUnitOfWork

    /// <summary>
    /// Represents an IUnitOfWork for Entity Framework
    /// </summary>
    public class EFUnitOfWork : IUnitOfWork
    {
        private DbContext _context;        
        private bool _disposed;
        public EFUnitOfWork(DbContext context)
        {
            if (context == null)
            {
                throw new UnitOfWorkException();
            }
            _context = context;
        }
        /// <summary>
        /// Method to be called when a UnitOfWork is to be committed.
        /// </summary>
        public void Commit()
        {
            _context.SaveChanges();
        }
        // Implement IDisposable.       
        public void Dispose()
        {
            Dispose(true);
            // Take yourself off the Finalization queue to prevent finalization code for object from executing a second time.
            GC.SuppressFinalize(this);
        }
       
        protected virtual void Dispose(bool disposing)
        {
            // Check to see if Dispose has already been called.
            if (!_disposed)
            {
                // If disposing equals true, dispose all managed and unmanaged resources.
                if (disposing)
                {
                    // Dispose managed resources.
                    if (_context != null)
                    {
                        _context.Dispose();
                    }
                }             
            }
            _disposed = true;
        }
    }
    [Serializable]
    public class UnitOfWorkException : Exception
    {
        public override string Message
        {
            get
            {
                return "The parameter must be EFUnitOfWork";
            }
        }
    }

The above code is at 
[Github](https://github.com/ashutoshraina/Pariksha/tree/master/Repository/Infrastructure "Github")

Are there alternatives ?
Oh yes plenty. Some just differ in implementation details , but others differ in philosophy altogether. Next time I will blog about a different one that I thought of initially. Then later I will write about the problems we face. Pick and choose what fits.

---
layout: page
lib: ccv
slug: ccv-memory
status: publish
title: lib/ccv_memory.c
desc: memory alloc/dealloc
categories:
- lib
---

ccv\_dense\_matrix\_renew
-------------------------

	ccv_dense_matrix_renew(ccv_dense_matrix_t *x, int rows, int cols, int types, int prefer_type, uint64_t sig)

Check the input matrix, if it is the allowed type, return it, otherwise create one with prefer\_type.

 * **x**: The matrix to check.
 * **rows**: Rows of the matrix.
 * **cols**: Columns of the matrix.
 * **types**: Allowed types, it can be a mask of multiple types, e.g. CCV\_8U \| CCV\_32S allows both 8-bit unsigned integer type and 32-bit signed integer type.
 * **prefer\_type**: The default type, it can be only one type.
 * **sig**: The signature, using 0 if you don't know what it is.

**return**: The returned matrix object that satisfies the requirements.

ccv\_dense\_matrix\_new
-----------------------

	ccv_dense_matrix_new(int rows, int cols, int type, void *data, uint64_t sig)

Create a dense matrix with rows, cols, and type.

 * **rows**: Rows of the matrix.
 * **cols**: Columns of the matrix.
 * **type**: Matrix supports 4 data types - CCV\_8U, CCV\_32S, CCV\_64S, CCV\_32F, CCV\_64F and up to 255 channels. e.g. CCV\_32F \| 31 will create a matrix with float (32-bit float point) data type with 31 channels (the default type for ccv\_hog).
 * **data**: If 0, ccv will create the matrix by allocating memory itself. Otherwise, it will use the memory region referenced by 'data'.
 * **sig**: The signature, using 0 if you don't know what it is.

**return**: The newly created matrix object.

ccv\_dense\_matrix
------------------

	ccv_dense_matrix_t ccv_dense_matrix(int rows, int cols, int type, void *data, uint64_t sig)

This method will return a dense matrix allocated on stack, with a data pointer to a custom memory region.

 * **rows**: Rows of the matrix.
 * **cols**: Columns of the matrix.
 * **type**: The type of matrix.
 * **data**: The data pointer that stores the actual matrix, it cannot be 0.
 * **sig**: The signature, using 0 if you don't know what it is.

**return**: static matrix structs.

ccv\_make\_matrix\_mutable
--------------------------

	void ccv_make_matrix_mutable(ccv_matrix_t *mat)

Mark the current matrix as mutable. Under the hood, it will set matrix signature to 0, and mark the matrix as non-collectable.

 * **mat**: The supplied matrix that will be marked as mutable.

ccv\_make\_matrix\_immutable
----------------------------

	void ccv_make_matrix_immutable(ccv_matrix_t *mat)

Mark the current matrix as immutable. Under the hood, it will generate a signature for the matrix, and mark it as non-collectable. For the convention, if the matrix is marked as immutable, you shouldn't change the content of the matrix, otherwise it will cause surprising behavior. If you want to change the content of the matrix, mark it as mutable first.

 * **mat**: The supplied matrix that will be marked as immutable.

ccv\_sparse\_matrix\_new
------------------------

	ccv_sparse_matrix_new(int rows, int cols, int type, int major, uint64_t sig)

Create a sparse matrix. ccv uses a double hash table for memory-efficient and quick-access sparse matrix.

 * **rows**: Rows of the matrix.
 * **cols**: Columns of the matrix.
 * **type**: The type of the matrix, the same as dense matrix.
 * **major**: Either CCV\_SPARSE\_ROW\_MAJOR or CCV\_SPARSE\_COL\_MAJOR, it determines the underlying data structure of the sparse matrix (either using row or column as the first-level hash table).
 * **sig**: The signature, using 0 if you don't know what it is.

**return**: The newly created sparse matrix object.

ccv\_matrix\_free\_immediately
------------------------------

	void ccv_matrix_free_immediately(ccv_matrix_t *mat)

Skip garbage-collecting process and free the matrix immediately.

 * **mat**: The matrix.

ccv\_matrix\_free
-----------------

	void ccv_matrix_free(ccv_matrix_t *mat)

In principal, you should always use this method to free a matrix. If you enabled cache in ccv, this method won't immediately free up memory space of the matrix. Instead, it will push the matrix to a cache if applicable so that if you want to create the same matrix again, ccv can shortcut the required matrix/image processing and return it from the cache.

 * **mat**: The matrix.

ccv\_cache\_generate\_signature
-------------------------------

	uint64_t ccv_cache_generate_signature(const char *msg, int len, uint64_t sig_start,...)

Generate a matrix signature based on input message and other signatures. This is the core method for ccv cache. In short, ccv does a given image processing by first generating an appropriate signature for that operation. It requires 1). an operation-specific message, which can be generated by concatenate the operation name and parameters. 2). the signature of input matrix(es). After that, ccv will look-up matrix in cache with the newly generated signature. If it exists, ccv will return that matrix and skip the whole operation.

 * **msg**: The concatenated message.
 * **len**: Message length.
 * **sig\_start**: The input matrix(es) signature, end the list with 0.

**return**: The generated 64-bit signature.

ccv\_drain\_cache
-----------------

	void ccv_drain_cache(void)

Drain up the cache.

ccv\_disable\_cache
-------------------

	void ccv_disable_cache(void)

Drain up and disable the application-wide cache.

ccv\_enable\_default\_cache
---------------------------

	void ccv_enable_default_cache(void)

Enable a application-wide cache for ccv at default memory bound (64MiB).

ccv\_enable\_cache
------------------

	void ccv_enable_cache(size_t size)

Enable a application-wide cache for ccv. The cache is bounded by given memory size.

 * **size**: The upper limit of the cache, in bytes.

CCV\_SPARSE\_FOREACH
--------------------

	CCV_SPARSE_FOREACH(mat, block)

This method enables you to loop over non-zero (or assigned) positions in a sparse matrix.

 * **mat**: The sparse matrix.
 * **block(row, col, value)**: a macro to loop over, row, col is the position (depends on if it is row major or col major), value is the typed array.

CCV\_SPARSE\_VECTOR\_FOREACH
----------------------------

	CCV_SPARSE_VECTOR_FOREACH(mat, vector, block)

This method enables you to loop over non-zero (or assigned) positions in one vector of a sparse matrix (you can use ccv\_get\_sparse\_matrix\_vector method)

 * **mat**: The sparse matrix.
 * **vector**: The vector within the sparse matrix.
 * **block(index, value)**: a macro to loop over, index is the position (depends on if it is row major or col major), value is the typed array.

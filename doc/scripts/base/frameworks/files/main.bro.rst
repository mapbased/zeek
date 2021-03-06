:tocdepth: 3

base/frameworks/files/main.bro
==============================
.. bro:namespace:: Files

An interface for driving the analysis of files, possibly independent of
any network protocol over which they're transported.

:Namespace: Files
:Imports: :doc:`base/bif/file_analysis.bif.bro </scripts/base/bif/file_analysis.bif.bro>`, :doc:`base/frameworks/analyzer </scripts/base/frameworks/analyzer/index>`, :doc:`base/frameworks/logging </scripts/base/frameworks/logging/index>`, :doc:`base/utils/site.bro </scripts/base/utils/site.bro>`

Summary
~~~~~~~
Runtime Options
###############
======================================================================== ========================================
:bro:id:`Files::enable_reassembler`: :bro:type:`bool` :bro:attr:`&redef` The default setting for file reassembly.
======================================================================== ========================================

Redefinable Options
###################
======================================================================================== ================================================================
:bro:id:`Files::analyze_by_mime_type_automatically`: :bro:type:`bool` :bro:attr:`&redef` Decide if you want to automatically attached analyzers to 
                                                                                         files based on the detected mime type of the file.
:bro:id:`Files::disable`: :bro:type:`table` :bro:attr:`&redef`                           A table that can be used to disable file analysis completely for
                                                                                         any files transferred over given network protocol analyzers.
:bro:id:`Files::reassembly_buffer_size`: :bro:type:`count` :bro:attr:`&redef`            The default per-file reassembly buffer size.
:bro:id:`Files::salt`: :bro:type:`string` :bro:attr:`&redef`                             The salt concatenated to unique file handle strings generated by
                                                                                         :bro:see:`get_file_handle` before hashing them in to a file id
                                                                                         (the *id* field of :bro:see:`fa_file`).
======================================================================================== ================================================================

Types
#####
====================================================================== ==============================================================
:bro:type:`Files::AnalyzerArgs`: :bro:type:`record` :bro:attr:`&redef` A structure which parameterizes a type of file analysis.
:bro:type:`Files::Info`: :bro:type:`record` :bro:attr:`&redef`         Contains all metadata related to the analysis of a given file.
:bro:type:`Files::ProtoRegistration`: :bro:type:`record`               
====================================================================== ==============================================================

Redefinitions
#############
========================================================== =
:bro:type:`Log::ID`: :bro:type:`enum`                      
:bro:type:`fa_file`: :bro:type:`record` :bro:attr:`&redef` 
========================================================== =

Events
######
============================================= ====================================================================
:bro:id:`Files::log_files`: :bro:type:`event` Event that can be handled to access the Info record as it is sent on
                                              to the logging framework.
============================================= ====================================================================

Functions
#########
===================================================================== =============================================================================
:bro:id:`Files::add_analyzer`: :bro:type:`function`                   Adds an analyzer to the analysis of a given file.
:bro:id:`Files::all_registered_mime_types`: :bro:type:`function`      Returns a table of all MIME-type-to-analyzer mappings currently registered.
:bro:id:`Files::analyzer_name`: :bro:type:`function`                  Translates a file analyzer enum value to a string with the
                                                                      analyzer's name.
:bro:id:`Files::describe`: :bro:type:`function`                       Provides a text description regarding metadata of the file.
:bro:id:`Files::disable_reassembly`: :bro:type:`function`             Disables the file reassembler on this file.
:bro:id:`Files::enable_reassembly`: :bro:type:`function`              Allows the file reassembler to be used if it's necessary because the
                                                                      file is transferred out of order.
:bro:id:`Files::file_exists`: :bro:type:`function`                    Lookup to see if a particular file id exists and is still valid.
:bro:id:`Files::lookup_file`: :bro:type:`function`                    Lookup an :bro:see:`fa_file` record with the file id.
:bro:id:`Files::register_analyzer_add_callback`: :bro:type:`function` Register a callback for file analyzers to use if they need to do some
                                                                      manipulation when they are being added to a file before the core code
                                                                      takes over.
:bro:id:`Files::register_for_mime_type`: :bro:type:`function`         Registers a MIME type for an analyzer.
:bro:id:`Files::register_for_mime_types`: :bro:type:`function`        Registers a set of MIME types for an analyzer.
:bro:id:`Files::register_protocol`: :bro:type:`function`              Register callbacks for protocols that work with the Files framework.
:bro:id:`Files::registered_mime_types`: :bro:type:`function`          Returns a set of all MIME types currently registered for a specific analyzer.
:bro:id:`Files::remove_analyzer`: :bro:type:`function`                Removes an analyzer from the analysis of a given file.
:bro:id:`Files::set_reassembly_buffer_size`: :bro:type:`function`     Set the maximum size the reassembly buffer is allowed to grow
                                                                      for the given file.
:bro:id:`Files::set_timeout_interval`: :bro:type:`function`           Sets the *timeout_interval* field of :bro:see:`fa_file`, which is
                                                                      used to determine the length of inactivity that is allowed for a file
                                                                      before internal state related to it is cleaned up.
:bro:id:`Files::stop`: :bro:type:`function`                           Stops/ignores any further analysis of a given file.
===================================================================== =============================================================================


Detailed Interface
~~~~~~~~~~~~~~~~~~
Runtime Options
###############
.. bro:id:: Files::enable_reassembler

   :Type: :bro:type:`bool`
   :Attributes: :bro:attr:`&redef`
   :Default: ``T``

   The default setting for file reassembly.

Redefinable Options
###################
.. bro:id:: Files::analyze_by_mime_type_automatically

   :Type: :bro:type:`bool`
   :Attributes: :bro:attr:`&redef`
   :Default: ``T``

   Decide if you want to automatically attached analyzers to 
   files based on the detected mime type of the file.

.. bro:id:: Files::disable

   :Type: :bro:type:`table` [:bro:type:`Files::Tag`] of :bro:type:`bool`
   :Attributes: :bro:attr:`&redef`
   :Default: ``{}``

   A table that can be used to disable file analysis completely for
   any files transferred over given network protocol analyzers.

.. bro:id:: Files::reassembly_buffer_size

   :Type: :bro:type:`count`
   :Attributes: :bro:attr:`&redef`
   :Default: ``524288``

   The default per-file reassembly buffer size.

.. bro:id:: Files::salt

   :Type: :bro:type:`string`
   :Attributes: :bro:attr:`&redef`
   :Default: ``"I recommend changing this."``

   The salt concatenated to unique file handle strings generated by
   :bro:see:`get_file_handle` before hashing them in to a file id
   (the *id* field of :bro:see:`fa_file`).
   Provided to help mitigate the possibility of manipulating parts of
   network connections that factor in to the file handle in order to
   generate two handles that would hash to the same file id.

Types
#####
.. bro:type:: Files::AnalyzerArgs

   :Type: :bro:type:`record`

      chunk_event: :bro:type:`event` (f: :bro:type:`fa_file`, data: :bro:type:`string`, off: :bro:type:`count`) :bro:attr:`&optional`
         An event which will be generated for all new file contents,
         chunk-wise.  Used when *tag* (in the
         :bro:see:`Files::add_analyzer` function) is
         :bro:see:`Files::ANALYZER_DATA_EVENT`.

      stream_event: :bro:type:`event` (f: :bro:type:`fa_file`, data: :bro:type:`string`) :bro:attr:`&optional`
         An event which will be generated for all new file contents,
         stream-wise.  Used when *tag* is
         :bro:see:`Files::ANALYZER_DATA_EVENT`.

      extract_filename: :bro:type:`string` :bro:attr:`&optional`
         (present if :doc:`/scripts/base/files/extract/main.bro` is loaded)

         The local filename to which to write an extracted file.
         This field is used in the core by the extraction plugin
         to know where to write the file to.  If not specified, then
         a filename in the format "extract-<source>-<id>" is
         automatically assigned (using the *source* and *id*
         fields of :bro:see:`fa_file`).

      extract_limit: :bro:type:`count` :bro:attr:`&default` = :bro:see:`FileExtract::default_limit` :bro:attr:`&optional`
         (present if :doc:`/scripts/base/files/extract/main.bro` is loaded)

         The maximum allowed file size in bytes of *extract_filename*.
         Once reached, a :bro:see:`file_extraction_limit` event is
         raised and the analyzer will be removed unless
         :bro:see:`FileExtract::set_limit` is called to increase the
         limit.  A value of zero means "no limit".
   :Attributes: :bro:attr:`&redef`

   A structure which parameterizes a type of file analysis.

.. bro:type:: Files::Info

   :Type: :bro:type:`record`

      ts: :bro:type:`time` :bro:attr:`&log`
         The time when the file was first seen.

      fuid: :bro:type:`string` :bro:attr:`&log`
         An identifier associated with a single file.

      tx_hosts: :bro:type:`set` [:bro:type:`addr`] :bro:attr:`&default` = ``{  }`` :bro:attr:`&optional` :bro:attr:`&log`
         If this file was transferred over a network
         connection this should show the host or hosts that
         the data sourced from.

      rx_hosts: :bro:type:`set` [:bro:type:`addr`] :bro:attr:`&default` = ``{  }`` :bro:attr:`&optional` :bro:attr:`&log`
         If this file was transferred over a network
         connection this should show the host or hosts that
         the data traveled to.

      conn_uids: :bro:type:`set` [:bro:type:`string`] :bro:attr:`&default` = ``{  }`` :bro:attr:`&optional` :bro:attr:`&log`
         Connection UIDs over which the file was transferred.

      source: :bro:type:`string` :bro:attr:`&log` :bro:attr:`&optional`
         An identification of the source of the file data.  E.g. it
         may be a network protocol over which it was transferred, or a
         local file path which was read, or some other input source.

      depth: :bro:type:`count` :bro:attr:`&default` = ``0`` :bro:attr:`&optional` :bro:attr:`&log`
         A value to represent the depth of this file in relation
         to its source.  In SMTP, it is the depth of the MIME
         attachment on the message.  In HTTP, it is the depth of the
         request within the TCP connection.

      analyzers: :bro:type:`set` [:bro:type:`string`] :bro:attr:`&default` = ``{  }`` :bro:attr:`&optional` :bro:attr:`&log`
         A set of analysis types done during the file analysis.

      mime_type: :bro:type:`string` :bro:attr:`&log` :bro:attr:`&optional`
         A mime type provided by the strongest file magic signature
         match against the *bof_buffer* field of :bro:see:`fa_file`,
         or in the cases where no buffering of the beginning of file
         occurs, an initial guess of the mime type based on the first
         data seen.

      filename: :bro:type:`string` :bro:attr:`&log` :bro:attr:`&optional`
         A filename for the file if one is available from the source
         for the file.  These will frequently come from
         "Content-Disposition" headers in network protocols.

      duration: :bro:type:`interval` :bro:attr:`&log` :bro:attr:`&default` = ``0 secs`` :bro:attr:`&optional`
         The duration the file was analyzed for.

      local_orig: :bro:type:`bool` :bro:attr:`&log` :bro:attr:`&optional`
         If the source of this file is a network connection, this field
         indicates if the data originated from the local network or not as
         determined by the configured :bro:see:`Site::local_nets`.

      is_orig: :bro:type:`bool` :bro:attr:`&log` :bro:attr:`&optional`
         If the source of this file is a network connection, this field
         indicates if the file is being sent by the originator of the
         connection or the responder.

      seen_bytes: :bro:type:`count` :bro:attr:`&log` :bro:attr:`&default` = ``0`` :bro:attr:`&optional`
         Number of bytes provided to the file analysis engine for the file.

      total_bytes: :bro:type:`count` :bro:attr:`&log` :bro:attr:`&optional`
         Total number of bytes that are supposed to comprise the full file.

      missing_bytes: :bro:type:`count` :bro:attr:`&log` :bro:attr:`&default` = ``0`` :bro:attr:`&optional`
         The number of bytes in the file stream that were completely missed
         during the process of analysis e.g. due to dropped packets.

      overflow_bytes: :bro:type:`count` :bro:attr:`&log` :bro:attr:`&default` = ``0`` :bro:attr:`&optional`
         The number of bytes in the file stream that were not delivered to
         stream file analyzers.  This could be overlapping bytes or 
         bytes that couldn't be reassembled.

      timedout: :bro:type:`bool` :bro:attr:`&log` :bro:attr:`&default` = ``F`` :bro:attr:`&optional`
         Whether the file analysis timed out at least once for the file.

      parent_fuid: :bro:type:`string` :bro:attr:`&log` :bro:attr:`&optional`
         Identifier associated with a container file from which this one was
         extracted as part of the file analysis.

      md5: :bro:type:`string` :bro:attr:`&log` :bro:attr:`&optional`
         (present if :doc:`/scripts/base/files/hash/main.bro` is loaded)

         An MD5 digest of the file contents.

      sha1: :bro:type:`string` :bro:attr:`&log` :bro:attr:`&optional`
         (present if :doc:`/scripts/base/files/hash/main.bro` is loaded)

         A SHA1 digest of the file contents.

      sha256: :bro:type:`string` :bro:attr:`&log` :bro:attr:`&optional`
         (present if :doc:`/scripts/base/files/hash/main.bro` is loaded)

         A SHA256 digest of the file contents.

      x509: :bro:type:`X509::Info` :bro:attr:`&optional`
         (present if :doc:`/scripts/base/files/x509/main.bro` is loaded)

         Information about X509 certificates. This is used to keep
         certificate information until all events have been received.

      extracted: :bro:type:`string` :bro:attr:`&optional` :bro:attr:`&log`
         (present if :doc:`/scripts/base/files/extract/main.bro` is loaded)

         Local filename of extracted file.

      extracted_cutoff: :bro:type:`bool` :bro:attr:`&optional` :bro:attr:`&log`
         (present if :doc:`/scripts/base/files/extract/main.bro` is loaded)

         Set to true if the file being extracted was cut off
         so the whole file was not logged.

      extracted_size: :bro:type:`count` :bro:attr:`&optional` :bro:attr:`&log`
         (present if :doc:`/scripts/base/files/extract/main.bro` is loaded)

         The number of bytes extracted to disk.

      entropy: :bro:type:`double` :bro:attr:`&log` :bro:attr:`&optional`
         (present if :doc:`/scripts/policy/frameworks/files/entropy-test-all-files.bro` is loaded)

         The information density of the contents of the file, 
         expressed as a number of bits per character. 
   :Attributes: :bro:attr:`&redef`

   Contains all metadata related to the analysis of a given file.
   For the most part, fields here are derived from ones of the same name
   in :bro:see:`fa_file`.

.. bro:type:: Files::ProtoRegistration

   :Type: :bro:type:`record`

      get_file_handle: :bro:type:`function` (c: :bro:type:`connection`, is_orig: :bro:type:`bool`) : :bro:type:`string`
         A callback to generate a file handle on demand when
         one is needed by the core.

      describe: :bro:type:`function` (f: :bro:type:`fa_file`) : :bro:type:`string` :bro:attr:`&default` = :bro:type:`function` :bro:attr:`&optional`
         A callback to "describe" a file.  In the case of an HTTP
         transfer the most obvious description would be the URL.
         It's like an extremely compressed version of the normal log.


Events
######
.. bro:id:: Files::log_files

   :Type: :bro:type:`event` (rec: :bro:type:`Files::Info`)

   Event that can be handled to access the Info record as it is sent on
   to the logging framework.

Functions
#########
.. bro:id:: Files::add_analyzer

   :Type: :bro:type:`function` (f: :bro:type:`fa_file`, tag: :bro:type:`Files::Tag`, args: :bro:type:`Files::AnalyzerArgs` :bro:attr:`&default` = ``[chunk_event=<uninitialized>, stream_event=<uninitialized>, extract_filename=<uninitialized>, extract_limit=104857600]`` :bro:attr:`&optional`) : :bro:type:`bool`

   Adds an analyzer to the analysis of a given file.
   

   :f: the file.
   

   :tag: the analyzer type.
   

   :args: any parameters the analyzer takes.
   

   :returns: true if the analyzer will be added, or false if analysis
            for the file isn't currently active or the *args*
            were invalid for the analyzer type.

.. bro:id:: Files::all_registered_mime_types

   :Type: :bro:type:`function` () : :bro:type:`table` [:bro:type:`Files::Tag`] of :bro:type:`set` [:bro:type:`string`]

   Returns a table of all MIME-type-to-analyzer mappings currently registered.
   

   :returns: A table mapping each analyzer to the set of MIME types
            registered for it.

.. bro:id:: Files::analyzer_name

   :Type: :bro:type:`function` (tag: :bro:type:`Files::Tag`) : :bro:type:`string`

   Translates a file analyzer enum value to a string with the
   analyzer's name.
   

   :tag: The analyzer tag.
   

   :returns: The analyzer name corresponding to the tag.

.. bro:id:: Files::describe

   :Type: :bro:type:`function` (f: :bro:type:`fa_file`) : :bro:type:`string`

   Provides a text description regarding metadata of the file.
   For example, with HTTP it would return a URL.
   

   :f: The file to be described.
   

   :returns: a text description regarding metadata of the file.

.. bro:id:: Files::disable_reassembly

   :Type: :bro:type:`function` (f: :bro:type:`fa_file`) : :bro:type:`void`

   Disables the file reassembler on this file.  If the file is not 
   transferred out of order this will have no effect.
   

   :f: the file.

.. bro:id:: Files::enable_reassembly

   :Type: :bro:type:`function` (f: :bro:type:`fa_file`) : :bro:type:`void`

   Allows the file reassembler to be used if it's necessary because the
   file is transferred out of order.
   

   :f: the file.

.. bro:id:: Files::file_exists

   :Type: :bro:type:`function` (fuid: :bro:type:`string`) : :bro:type:`bool`

   Lookup to see if a particular file id exists and is still valid.
   

   :fuid: the file id.
   

   :returns: T if the file uid is known.

.. bro:id:: Files::lookup_file

   :Type: :bro:type:`function` (fuid: :bro:type:`string`) : :bro:type:`fa_file`

   Lookup an :bro:see:`fa_file` record with the file id.
   

   :fuid: the file id.
   

   :returns: the associated :bro:see:`fa_file` record.

.. bro:id:: Files::register_analyzer_add_callback

   :Type: :bro:type:`function` (tag: :bro:type:`Files::Tag`, callback: :bro:type:`function` (f: :bro:type:`fa_file`, args: :bro:type:`Files::AnalyzerArgs`) : :bro:type:`void`) : :bro:type:`void`

   Register a callback for file analyzers to use if they need to do some
   manipulation when they are being added to a file before the core code
   takes over.  This is unlikely to be interesting for users and should
   only be called by file analyzer authors but is *not required*.
   

   :tag: Tag for the file analyzer.
   

   :callback: Function to execute when the given file analyzer is being added.

.. bro:id:: Files::register_for_mime_type

   :Type: :bro:type:`function` (tag: :bro:type:`Files::Tag`, mt: :bro:type:`string`) : :bro:type:`bool`

   Registers a MIME type for an analyzer. If a future file with this type is seen,
   the analyzer will be automatically assigned to parsing it. The function *adds*
   to all MIME types already registered, it doesn't replace them.
   

   :tag: The tag of the analyzer.
   

   :mt: The MIME type in the form "foo/bar" (case-insensitive).
   

   :returns: True if the MIME type was successfully registered.

.. bro:id:: Files::register_for_mime_types

   :Type: :bro:type:`function` (tag: :bro:type:`Files::Tag`, mime_types: :bro:type:`set` [:bro:type:`string`]) : :bro:type:`bool`

   Registers a set of MIME types for an analyzer. If a future connection on one of
   these types is seen, the analyzer will be automatically assigned to parsing it.
   The function *adds* to all MIME types already registered, it doesn't replace
   them.
   

   :tag: The tag of the analyzer.
   

   :mts: The set of MIME types, each in the form "foo/bar" (case-insensitive).
   

   :returns: True if the MIME types were successfully registered.

.. bro:id:: Files::register_protocol

   :Type: :bro:type:`function` (tag: :bro:type:`Analyzer::Tag`, reg: :bro:type:`Files::ProtoRegistration`) : :bro:type:`bool`

   Register callbacks for protocols that work with the Files framework.
   The callbacks must uniquely identify a file and each protocol can 
   only have a single callback registered for it.
   

   :tag: Tag for the protocol analyzer having a callback being registered.
   

   :reg: A :bro:see:`Files::ProtoRegistration` record.
   

   :returns: true if the protocol being registered was not previously registered.

.. bro:id:: Files::registered_mime_types

   :Type: :bro:type:`function` (tag: :bro:type:`Files::Tag`) : :bro:type:`set` [:bro:type:`string`]

   Returns a set of all MIME types currently registered for a specific analyzer.
   

   :tag: The tag of the analyzer.
   

   :returns: The set of MIME types.

.. bro:id:: Files::remove_analyzer

   :Type: :bro:type:`function` (f: :bro:type:`fa_file`, tag: :bro:type:`Files::Tag`, args: :bro:type:`Files::AnalyzerArgs` :bro:attr:`&default` = ``[chunk_event=<uninitialized>, stream_event=<uninitialized>, extract_filename=<uninitialized>, extract_limit=104857600]`` :bro:attr:`&optional`) : :bro:type:`bool`

   Removes an analyzer from the analysis of a given file.
   

   :f: the file.
   

   :tag: the analyzer type.
   

   :args: the analyzer (type and args) to remove.
   

   :returns: true if the analyzer will be removed, or false if analysis
            for the file isn't currently active.

.. bro:id:: Files::set_reassembly_buffer_size

   :Type: :bro:type:`function` (f: :bro:type:`fa_file`, max: :bro:type:`count`) : :bro:type:`void`

   Set the maximum size the reassembly buffer is allowed to grow
   for the given file.
   

   :f: the file.
   

   :max: Maximum allowed size of the reassembly buffer.

.. bro:id:: Files::set_timeout_interval

   :Type: :bro:type:`function` (f: :bro:type:`fa_file`, t: :bro:type:`interval`) : :bro:type:`bool`

   Sets the *timeout_interval* field of :bro:see:`fa_file`, which is
   used to determine the length of inactivity that is allowed for a file
   before internal state related to it is cleaned up.  When used within
   a :bro:see:`file_timeout` handler, the analysis will delay timing out
   again for the period specified by *t*.
   

   :f: the file.
   

   :t: the amount of time the file can remain inactive before discarding.
   

   :returns: true if the timeout interval was set, or false if analysis
            for the file isn't currently active.

.. bro:id:: Files::stop

   :Type: :bro:type:`function` (f: :bro:type:`fa_file`) : :bro:type:`bool`

   Stops/ignores any further analysis of a given file.
   

   :f: the file.
   

   :returns: true if analysis for the given file will be ignored for the
            rest of its contents, or false if analysis for the file
            isn't currently active.


